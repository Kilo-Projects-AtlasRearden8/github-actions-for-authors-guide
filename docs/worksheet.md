# GitHub Actions Workflow Worksheet

Print this page and fill it in by hand before you create a workflow file.
One worksheet per workflow.

---

**Workflow name:** _______________________________________________________

**Repository:** __________________________________________________________

**Date:** ________________________   **Filled out by:** ____________________

---

## 1. When should it run?

Pick the event that starts your workflow:

- [ ] `push` — when code is pushed to a branch
- [ ] `pull_request` — when a pull request is opened or updated
- [ ] `schedule` — on a timer (like every morning)
- [ ] `workflow_dispatch` — when you click "Run workflow" on GitHub

Branch (usually `main` or `master`): ______________________________________

---

## 2. What computer should it use?

`runs-on:` _______________________________________________________________

Common choice: `ubuntu-latest` (works for most book and site builds)

---

## 3. Steps (in order)

Write each step your robot should do, one at a time.

**Step 1:** ______________________________________________________________
- What it does: __________________________________________________________
- Command or action: _____________________________________________________

**Step 2:** ______________________________________________________________
- What it does: __________________________________________________________
- Command or action: _____________________________________________________

**Step 3:** ______________________________________________________________
- What it does: __________________________________________________________
- Command or action: _____________________________________________________

**Step 4:** ______________________________________________________________
- What it does: __________________________________________________________
- Command or action: _____________________________________________________

**Step 5:** ______________________________________________________________
- What it does: __________________________________________________________
- Command or action: _____________________________________________________

---

## 4. Does it need a secret?

- [ ] No
- [ ] Yes — secret name: _________________________________________________

> Remember: add the secret in GitHub (Settings → Secrets and variables →
> Actions). Never paste it into a YAML file.

---

## 5. Where should the output go?

- [ ] Nothing (just runs checks)
- [ ] An artifact (downloadable from the Actions tab)
- [ ] GitHub Pages (publishes a live website)
- [ ] Other: ____________________________________________________________

---

## 6. After it runs, what should I check?

_________________________________________________________________________

_________________________________________________________________________

---

## Workflow skeleton (copy this into your .yml file)

```yaml
name: _______________________________________________

on:
  push:
    branches: [ ______________ ]

jobs:
  ______________:
    runs-on: __________________
    steps:
      - name: Check out repository
        uses: actions/checkout@v4

      - name: ________________________________________
        run: |
          ____________________________________________
          ____________________________________________

      - name: ________________________________________
        run: |
          ____________________________________________
          ____________________________________________
```

---

**Tip:** Start with one tiny workflow. Get it working, then add more steps.
Automation is easier one step at a time.

---

Created by Asdale Dynamics, LLC.
MIT License. Copyright (c) 2026 Asdale Dynamics, LLC.
