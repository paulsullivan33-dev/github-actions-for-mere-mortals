# Artifacts and Caching

Jobs run on fresh VMs and vanish — so how do files survive? Two
mechanisms, for two different needs: **artifacts** (keep the
outputs) and **caching** (skip repeated work).

## Artifacts: saving what a job made

```yaml
steps:
  - run: pytest --junitxml=report.xml
  - uses: actions/upload-artifact@v4
    with:
      name: test-report
      path: report.xml
      retention-days: 7
```

**Check (validated):** `report.xml` is downloadable from the run's
page for 7 days. Artifacts are for **outputs**: test reports, built
binaries, the docs site, screenshots of failures.

Pass files *between jobs* (remember: jobs are separate VMs):

```yaml
name: Build and deploy
on: push

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: ./build.sh
      - uses: actions/upload-artifact@v4
        with:
          name: dist
          path: dist/

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: dist
          path: dist/
      - run: ./deploy.sh
```

**Check (validated):** the build job uploads, the deploy job
downloads — one complete, parseable workflow.

This is the standard build-once-deploy-later pipeline: the build
job uploads, the deploy job downloads.

## Caching: skipping what hasn't changed

```yaml
- uses: actions/cache@v4
  with:
    path: ~/.cache/pip
    key: pip-${{ hashFiles('requirements.txt') }}
```

First run: cache miss, pip downloads everything, cache saved.
Later runs: `requirements.txt` unchanged → same key → cache hit →
dependencies restored in seconds. **Key insight:** the key must
change when the inputs change — `hashFiles(...)` of the lockfile
does exactly that.

| | Artifacts | Cache |
|---|---|---|
| Purpose | Keep outputs | Skip repeated work |
| Lifetime | Days (you set) | Until key changes / evicted |
| Between jobs | Yes (upload → download) | Yes (same key) |
| Human downloads | Yes | No |

## The gotchas

- **Cache poisoning:** a cache saved from a PR branch can be read
  by later runs — GitHub scopes caches per-branch to limit this,
  but don't cache secrets or signatures.
- **Cache size:** 10 GB per repo; old entries evict automatically.
  Fine for dependencies, not for "the whole build directory."
- **Artifacts aren't deployment:** they're downloads on a run
  page, not a hosted site. To *publish*, see
  [Deployments](deployments.md).

**Next:** [Pull Request Workflows](pull-requests.md)
