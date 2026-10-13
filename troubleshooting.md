# Troubleshooting

## The failures you'll actually hit

**Workflow doesn't run at all**

The file must be in `.github/workflows/` on the **default
branch** (usually `main`) — workflows on other branches only run
for events *on those branches*. Also check the Actions tab isn't
disabled (Settings → Actions → allow). And YAML indentation:
two spaces, no tabs — a misaligned `on:` silently parses as
something else.

**"This job failed" with no useful output**

Expand *every* step — the red mark shows where it stopped, but a
step can "succeed" while printing the real error (a test runner
exiting 0 on collection errors, a deploy script swallowing
failures). Add `set -euo pipefail` to `run:` scripts so failures
actually fail.

**Secrets "not working"**

Three classics: the secret name is misspelled (`secrets.PYPI_TOKEN`
vs `secrets.PyPI_Token` — case-sensitive); the secret is defined
at the *environment* level but the job doesn't name that
environment; or it's a fork PR (no secrets by design — see
[Secrets](secrets.md)).

**Cache never hits**

The key changed (check what's in `hashFiles(...)` — a lockfile
that churns every commit defeats the cache) or you're expecting a
cache from another branch (caches are branch-scoped; `main`'s
cache is the fallback). `actions/cache` prints hit/miss in the
logs — read it.

**Matrix explodes into dozens of jobs**

Count your dimensions (see [Matrix Builds](matrix-builds.md)) —
3×4×2 = 24 jobs is correct but expensive. `exclude:` the combos
you don't need, or split into "quick" (PRs) and "full" (nightly)
workflows.

**Minutes exhausted mid-month**

Private repos on the free plan: 2,000 Linux-minutes/month. Find
the hogs in Settings → Billing → Actions usage. Usual fixes:
`paths` filters so docs edits skip CI, smaller matrices on PRs
with full ones nightly, caching, and not running macOS runners
(10× multiplier) casually.

**"Resource not accessible by integration"**

The `GITHUB_TOKEN` lacks permission — add an explicit
`permissions:` block (see [Deployments](deployments.md)). The
default is increasingly restrictive, which is good, once you know
to ask.

**Scheduled workflow stopped running**

Disabled after 60 days of repo inactivity (see
[Triggers](triggers.md)) — push anything to wake it. Or the cron
expression is wrong: use a validator, remember it's **UTC**, and
note GitHub schedules can lag 15+ minutes under load.

**Two workflows, one push, chaos**

`on: push` in five workflow files = five runs per push. That's
fine — but if they deploy, they'll race. Consolidate deploy
triggers (tags, environments with approvals) and keep `push`
workflows to CI.

## The meta-lesson

Every Actions failure is one of three things: the **YAML** (syntax/
structure), the **environment** (runner, versions, secrets), or the
**code** (your tests genuinely fail). Diagnose in that order —
YAML first, because it's cheapest to check.

**Next:** [Glossary](glossary.md)
