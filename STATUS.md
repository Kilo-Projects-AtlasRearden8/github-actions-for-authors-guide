# STATUS

## What was built

A beginner manual and static HTML site for GitHub Actions, targeted at authors
and small publishers. Created by Asdale Dynamics, LLC.

Files in this folder:

- `LICENSE` — MIT License, copyright line exactly
  `Copyright (c) 2026 Asdale Dynamics, LLC`.
- `README.md` — project overview, credits Asdale Dynamics, LLC, states no
  secrets or personal data are stored here.
- `docs/github-actions-for-authors.md` — the full beginner manual: what GitHub
  Actions is, your first workflow ("Hello, book"), auto-building a book site
  from Markdown, running checks on pushes, deploying to GitHub Pages, safe
  secrets handling, troubleshooting, and a printable workflow worksheet.
- `docs/worksheet.md` — dedicated printable workflow worksheet (fill-in-the-blanks
  plus a YAML skeleton to copy into a `.yml` file).
- `site/index.html` — polished static HTML version of the manual with embedded
  CSS, navigation, callouts, tables, and print styles.
- `STATUS.md` — this file.

No `.github/workflows` directory is present (the examples are for the reader to
copy into their own repository). No API keys, passwords, secrets, or personal
data are stored in any file.

## How to run it

This is a documentation/static-site project. There is nothing to install or
execute to "run" the guide itself:

- Open `site/index.html` in any browser to read the static HTML version.
- Read `docs/github-actions-for-authors.md` and `docs/worksheet.md` in any
  Markdown reader or text editor.
- To use the workflow examples, copy a YAML block into
  `.github/workflows/<name>.yml` in your own GitHub repository and push it.
  Then open the repository's Actions tab to watch it run.

The YAML examples are written to be copy-pasteable and use only public,
versioned GitHub Actions (`actions/checkout@v4`, `actions/setup-python@v5`,
etc.) with placeholder names — no real secrets.

## Verification results

| Check | Result |
|-------|--------|
| LICENSE copyright line exact (`Copyright (c) 2026 Asdale Dynamics, LLC`) | Pass |
| README credits Asdale Dynamics, LLC | Pass |
| README states no secrets / no personal data | Pass |
| All 5 YAML fenced blocks parse with `yaml.safe_load` | Pass |
| Static HTML (`site/index.html`) parses with no structural errors | Pass |
| No `.github/workflows` directory present | Pass |
| No hardcoded API keys / passwords / secret values in any file | Pass |
| No personal data (emails, phones, addresses, real keys) beyond README credit | Pass |
| `git init` + commit | Pending (see below) |

Note: This project is documentation only, so there are no unit tests, build
steps, or a test suite to execute. Verification consisted of YAML parsing,
HTML parsing, and content scans for secrets/personal data.

## Known gaps

- The workflow YAML examples are not executed against a live GitHub repository
  (they cannot be run locally without GitHub Actions). They are written to be
  syntactically valid and copy-pasteable.
- No automated test suite or CI configuration is included by design (adding a
  `.github/workflows` folder was explicitly excluded).
- The Markdown-to-HTML build example uses `python -m markdown`, which requires
  the `markdown` package installed in the Actions environment (handled by a
  `pip install markdown` step in the example).
- The manual references `dylanasdale.github.io` and Dylan Asdale's background
  only in the README credit section; no personal contact data is stored.

## Next steps (for the reader)

1. Copy a workflow YAML block into `.github/workflows/` in your own repo.
2. Push to GitHub and watch the run in the Actions tab.
3. Set Pages source to "GitHub Actions" under Settings → Pages before the
   first deploy.
4. Add real secrets via Settings → Secrets and variables → Actions (never in
   YAML).
5. Use the printable worksheet (`docs/worksheet.md`) to plan each new workflow.

## Commit

Initialized git and committed all files with a plain message (no
Co-authored-by trailers). No remote created, nothing pushed, nothing published.
