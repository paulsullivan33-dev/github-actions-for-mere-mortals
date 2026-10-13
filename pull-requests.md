# Pull Request Workflows

The highest-value Actions pattern: **every PR gets tested
automatically, and merging is blocked until it's green.** The
computer says "not yet" so a human doesn't have to.

## The CI gate

```yaml
name: CI
on:
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: ./run-tests.sh
```

**Check (validated):** opened or updated PRs targeting `main`
trigger this. Then, in repo Settings → Branches → branch
protection for `main`: **require status checks to pass** and select
the job. Now the merge button stays disabled until CI is green.

## What good PR CI does

1. **Lint** — style nits as robot comments, not human arguments.
2. **Test** — the suite, on a clean VM, every time.
3. **Build** — does it even compile/package?
4. **Preview** — deploy the PR to a throwaway URL (docs sites,
   web apps) so reviewers *see* the change.

```yaml
jobs:
  preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: ./build-docs.sh
      - uses: actions/upload-artifact@v4
        with: {name: preview, path: site/}
```

(Or deploy to ephemeral hosting per-PR — Netlify/Vercel/Cloudflare
do this natively.)

## Auto-labels and housekeeping

```yaml
name: Label PRs
on: pull_request

jobs:
  label:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - uses: actions/labeler@v5
```

`actions/labeler` tags PRs by changed paths (`docs/**` → "docs").
Small thing, big readability win on busy repos. Note the explicit
`permissions:` — least privilege again (see [Secrets](secrets.md)).

## Draft PRs and CI cost

Draft PRs trigger workflows like any PR — on a big matrix that's
real minutes per "work in progress, don't review yet" push. Options:
`paths` filters (see [Triggers](triggers.md)), or check
`github.event.pull_request.draft` and skip expensive jobs for
drafts.

## The etiquette

- **Keep PR CI fast.** Under 10 minutes or developers route around
  it. Split slow suites; cache aggressively (see [Artifacts and
  Caching](artifacts-caching.md)).
- **Flaky tests are a CI emergency.** A suite that fails randomly
  trains everyone to ignore red — quarantine or fix flakes
  immediately.
- **Required checks are a promise.** If `main` requires green CI,
  never bypass it "just this once." The one time becomes the
  culture.

**Next:** [Deployments](deployments.md)
