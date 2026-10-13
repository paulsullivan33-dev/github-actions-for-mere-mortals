# Triggers

The `on:` block answers one question: **what starts this
workflow?** Pick the event that matches the job.

## The big four

```yaml
on: push
```

Every push to any branch. The default workhorse — run the tests,
always.

```yaml
on:
  pull_request:
    branches: [main]
```

When a PR targets `main` (opened, updated, reopened). This is the
**gatekeeper** pattern — see [Pull Request
Workflows](pull-requests.md).

```yaml
on:
  schedule:
    - cron: "0 9 * * *"
```

On a timer — cron syntax, in UTC. `0 9 * * *` is 9:00 AM UTC
daily. For nightly builds, dependency checks, price watches, the
"is the site still up?" probe.

```yaml
on: workflow_dispatch
```

A manual **"Run workflow"** button in the Actions tab. Add inputs
for parameters:

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Where to deploy"
        required: true
        default: "staging"
```

**Check (validated):** all four parse as valid trigger blocks.

## Combining and filtering

```yaml
on:
  push:
    branches: [main]
    paths:
      - "src/**"
```

Only pushes to `main` that touch `src/`. Docs-only edits skip the
test run — **paths** filtering saves real CI minutes.

```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened]
```

React only to specific PR activity. (The defaults already cover
the sane cases; reach for `types` when the defaults do something
you don't want.)

## Tags and releases

```yaml
on:
  push:
    tags: ["v*"]
```

Runs when you push a tag like `v1.2.3` — the classic "cut a
release" trigger. Pair with a deploy job and tagging becomes
shipping (see [Deployments](deployments.md)).

## The trigger cheat sheet

| I want... | Use |
|-----------|-----|
| Test every change | `on: push` |
| Gatekeep merges | `on: pull_request` |
| Nightly/scheduled work | `on: schedule` (cron, UTC!) |
| Manual runs with options | `on: workflow_dispatch` |
| Ship on version tags | `on: push: tags: ["v*"]` |
| Skip docs-only churn | `paths:` filter |

**Careful:** scheduled workflows on a quiet repo get **disabled
after 60 days** of no activity — GitHub pauses the cron. A push
wakes it back up.

**Next:** [Jobs, Steps, and Runners](jobs-steps-runners.md)
