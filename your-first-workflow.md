# Your First Workflow

A workflow is a YAML file in `.github/workflows/`. Here's a
complete one — it runs the test suite on every push:

```yaml
# .github/workflows/ci.yml
name: CI

on: push

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -r requirements.txt
      - run: pytest
```

**Check (validated):** this parses as valid YAML with the required
`on`, `jobs`, `runs-on`, and `steps` keys. Twenty lines, and your
repo now tests itself on every push.

## Reading it line by line

- `name: CI` — the label shown in GitHub's Actions tab.
- `on: push` — **trigger**: run whenever anyone pushes.
- `jobs:` → `test:` — one **job** named "test".
- `runs-on: ubuntu-latest` — **runner**: a fresh Ubuntu VM,
  provided by GitHub, discarded after the job.
- `steps:` — the recipe:
  - `uses: actions/checkout@v4` — borrow the "check out my code"
    action (see [The Marketplace](marketplace.md)).
  - `uses: actions/setup-python@v5` — borrow the "install Python"
    action, asking for 3.12.
  - `run:` — shell commands, executed in order, on the runner.

## The fresh-machine guarantee

Every job starts on a **brand-new VM** — nothing left over from
last time, no "well it worked on my laptop" mystery state. If it
passes here, it passes anywhere with the same setup. This is the
deep reason CI catches real bugs: the environment is honest.

## Your turn

1. Create `.github/workflows/ci.yml` in any repo with that
   content (adapt the last two steps to your project).
2. Commit and push.
3. Open the repo's **Actions** tab — watch your workflow run.

**Check:** green checkmark next to your commit. That's CI. The
rest of this guide is making that checkmark do more interesting
things.

**Next:** [Triggers](triggers.md)
