# Jobs, Steps, and Runners

A workflow is a tree: **workflow → jobs → steps**. Understanding
how the levels relate — especially what runs *where* and *in what
order* — prevents most confusion.

## Jobs: parallel by default

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: ruff check .

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: pytest
```

**Check (validated):** `lint` and `test` run **simultaneously** on
two separate VMs. Jobs don't share files, memory, or state — each
is a fresh machine. Fast (parallel), but isolated.

## Depending on another job

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: pytest

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: ./deploy.sh
```

`needs: test` — `deploy` waits for `test`, and **skips if `test`
fails**. This is the pipeline pattern: test → build → deploy, each
gated on the last. (`needs` also accepts a list: `needs: [lint,
test]`.)

## Steps: sequential within a job

```yaml
steps:
  - name: Check out code
    uses: actions/checkout@v4

  - name: Run tests
    run: pytest -x -q

  - name: Upload report
    if: failure()
    uses: actions/upload-artifact@v4
    with:
      name: test-report
      path: report.xml
```

Steps run **in order, on the same machine**, sharing the filesystem.
Each step gets a `name` (shown in the UI — write good ones, future
you reads these logs). `run:` is a shell script; `uses:` borrows
an action; `with:` passes it parameters; `if: failure()` runs a
step only when earlier steps failed (see [Debugging](debugging.md)).

## Runners: whose computer?

| Runner | What it is | Cost |
|--------|-----------|------|
| `ubuntu-latest` | GitHub-hosted Linux VM | Free minutes included |
| `windows-latest` | GitHub-hosted Windows VM | 2× minutes |
| `macos-latest` | GitHub-hosted macOS VM | 10× minutes |
| self-hosted | Your own machine | Your electricity |

Public repos get generous free minutes; private repos on the free
plan get 2,000/month (Linux minutes — Windows/macOS multiply).
**Self-hosted runners** are for special hardware, private
networks, or heavy use — powerful, but now *you* maintain the
machine.

## The mental model

```text
workflow "CI"
├── job "lint"   → VM #1 → step → step → step
└── job "test"   → VM #2 → step → step → step   (parallel!)
```

Jobs are machines; steps are commands on one machine. When a
workflow confuses you, ask: "which machine is this step on, and
what's on its disk?" — the answer is usually "a fresh VM with only
what previous steps in *this job* put there."

**Next:** [The Marketplace](marketplace.md)
