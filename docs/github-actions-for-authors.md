# GitHub Actions for Authors: A Beginner Manual

**Plain English, step by step, never condescending.**  
Written for authors and small publishers who want their websites and checks to
run themselves. Created by Asdale Dynamics, LLC.

---

## What is GitHub Actions?

GitHub Actions is a free tool built into GitHub that runs tasks for you
automatically. Think of it like a robot assistant that lives in your
repository. You tell it what to do, when to do it, and it does the work — no
one has to click a button.

Common jobs for authors:
- **Build your book site** every time you push a new chapter.
- **Run checks** on pushes (make sure links work, spelling looks OK, files are
  formatted right).
- **Deploy to GitHub Pages** so your book is live on the web for free.
- **Handle secrets safely** (API keys, publishing tokens) without ever pasting
  them into a file.

All of this is defined in a YAML file. YAML is just a plain-text format that
looks like a list with indentation. If you can write an email, you can write
YAML.

---

## Where workflow files live

Workflow files go in a folder called `.github/workflows/` inside your
repository. Each `.yml` or `.yaml` file in that folder is one automated job.

> **Important:** This repository does not contain a `.github/workflows/` folder.
> That's intentional. The examples below are for you to copy into *your own*
> repository, not to run here.

---

## Your first workflow: "Hello, book"

Here is the smallest useful workflow. It runs whenever you push code and prints
a friendly message.

Create `.github/workflows/hello.yml` in your repository:

```yaml
name: Hello, book

on:
  push:
    branches: [ main ]

jobs:
  greet:
    runs-on: ubuntu-latest
    steps:
      - name: Say hello
        run: echo "Hello from your book site!"
```

### What each line means

| Line | Meaning |
|------|---------|
| `name:` | A friendly name for your workflow, shown on the GitHub Actions tab. |
| `on:` | The event that starts the workflow. `push` means "when code is pushed." |
| `branches:` | Only run on the `main` branch (change this to match your default branch). |
| `jobs:` | A group of tasks. You can have one job or many. |
| `greet:` | A name you give this job. Pick anything you like. |
| `runs-on:` | The kind of computer GitHub provides. `ubuntu-latest` is standard. |
| `steps:` | The list of actions to do, in order. |
| `run:` | A shell command to execute. |

Push this file to GitHub, then go to your repository's **Actions** tab. You
should see your workflow run and print "Hello from your book site!"

---

## Auto-building a book site

Many authors write their books in Markdown (`.md` files) and want a website
that updates automatically. This workflow builds a simple static HTML site from
your Markdown files every time you push.

Create `.github/workflows/build-book.yml`:

```yaml
name: Build book site

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install a Markdown converter
        run: pip install markdown

      - name: Build HTML from Markdown
        run: |
          mkdir -p _site
          for file in *.md; do
            [ -e "$file" ] || continue
            python -m markdown "$file" > "_site/${file%.md}.html"
          done

      - name: Upload site as artifact
        uses: actions/upload-artifact@v4
        with:
          name: book-site
          path: _site
```

### How it works

1. `actions/checkout@v4` downloads your repository so the robot can read your
   Markdown files.
2. `actions/setup-python@v5` installs Python so we can convert Markdown to HTML.
3. The `run:` block loops over every `.md` file, converts it to HTML, and saves
   it in a folder called `_site/`.
4. `actions/upload-artifact@v4` saves the built site so you can download it or
   deploy it in a later step.

You can download the artifact from the **Actions** tab on GitHub to see your
built site.

---

## Running checks on pushes

You can run automatic checks every time someone pushes, so you catch problems
early. This example checks that Markdown files have titles and that no file is
too large.

Create `.github/workflows/checks.yml`:

```yaml
name: Run checks

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Check Markdown files have titles
        run: |
          for file in *.md; do
            [ -e "$file" ] || continue
            if ! head -1 "$file" | grep -q '^# '; then
              echo "::error file=$file::Missing title (start with # Title)"
              exit 1
            fi
          done

      - name: Check no file is too large
        run: |
          big=$(find . -type f -size +5M -not -path './.git/*' | head -n 1)
          if [ -n "$big" ]; then
            echo "::error::File too large: $big"
            exit 1
          fi
```

### What this does

- Runs on both `push` and `pull_request`, so checks happen before and after
  changes are merged.
- `::error` annotations show up directly in the GitHub UI, pointing at the
  exact file and line with a problem.
- If any step exits with `exit 1`, the whole check fails and GitHub shows a red
  X. This protects your main branch from broken changes.

---

## Deploying to GitHub Pages

GitHub Pages hosts your site for free at `https://yourusername.github.io/your-repo/`.
This workflow builds your site and publishes it to Pages automatically.

Create `.github/workflows/deploy-pages.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install Markdown converter
        run: pip install markdown

      - name: Build HTML site
        run: |
          mkdir -p _site
          for file in *.md; do
            [ -e "$file" ] || continue
            python -m markdown "$file" > "_site/${file%.md}.html"
          done

      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: _site

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### Before you deploy

1. In your repository on GitHub, go to **Settings → Pages**.
2. Under "Build and deployment," set **Source** to "GitHub Actions."
3. Make sure the default branch is the one your workflow uses (usually `main`).

The first time you push this workflow, GitHub will create a deployment. After
it finishes, your site is live. Every push to `main` rebuilds and redeploys it.

---

## Safe secrets handling

Sometimes a workflow needs a password, API key, or token — for example, to
publish to a book store or send a notification. **Never put secrets in a YAML
file.** Anyone who can see your repository can see the file.

### Repository secrets (the right way)

1. In your repository on GitHub, go to **Settings → Secrets and variables →
   Actions**.
2. Click **New repository secret**.
3. Give it a name (for example, `BOOK_STORE_API_KEY`) and paste the value.
4. Click **Add secret**.

Then reference it in your workflow:

```yaml
      - name: Publish to book store
        run: echo "Publishing with key of length ${#BOOK_STORE_API_KEY}"
        env:
          BOOK_STORE_API_KEY: ${{ secrets.BOOK_STORE_API_KEY }}
```

### Key rules

| Do | Don't |
|----|-------|
| Store secrets in GitHub's secret settings | Paste secrets into YAML files |
| Use `${{ secrets.SECRET_NAME }}` only inside `env:` or step inputs | Log or print secret values |
| Use different secrets for different repositories | Reuse one personal key everywhere |
| Limit each secret to what it needs to do | Give one key permission to everything |

### Environments (extra safety)

For deploys, use a GitHub Environment (like `github-pages` shown above).
Environments can require approval before a workflow runs, so a deploy doesn't
happen until someone clicks "Approve." This is a great safety net for author
sites.

---

## Printable workflow worksheet

Print this page or copy it into a notebook. Fill in the blanks before you
create a workflow file.

```
Workflow name: _______________________________________________

When should it run? (push, pull_request, schedule, manual)
  Event: _____________________________________________________
  Branch: ____________________________________________________

What computer should it use? (ubuntu-latest, windows-latest, macOS-latest)
  runs-on: ___________________________________________________

Steps (list each step in order):
  1. _________________________________________________________
     Command or action: _______________________________________

  2. _________________________________________________________
     Command or action: _______________________________________

  3. _________________________________________________________
     Command or action: _______________________________________

Does it need a secret? If yes, name it: ________________________

Where should the output go? (artifact, Pages, nothing)
  Destination: _______________________________________________

After it runs, what should I check?
  ____________________________________________________________
```

**Tip:** Start with one tiny workflow (like "Hello, book") and add more steps
once it works. Automation is easier when you build it one step at a time.

---

## Troubleshooting

| Problem | What to try |
|---------|-------------|
| Workflow doesn't appear | Check the file is in `.github/workflows/` and ends in `.yml` or `.yaml`. |
| Red X on a run | Click the run in the Actions tab, then click the failed step to see its logs. |
| "Permission denied" deploying | Go to Settings → Pages and set the source to "GitHub Actions." |
| Secret not found | Make sure the secret name matches exactly (case-sensitive) and is spelled the same in the workflow. |
| Build works locally but not in Actions | The Actions computer may not have the tools you use. Install them in a `run:` step. |

---

## Keep learning

- GitHub Actions documentation: docs.github.com/actions
- GitHub Pages: pages.github.com
- Markdown guide: markdown.org

Start small, break things, fix them, and keep going. Your book site can build
itself while you write.

---

**Created by Asdale Dynamics, LLC.**  
MIT License. Copyright (c) 2026 Asdale Dynamics, LLC.
