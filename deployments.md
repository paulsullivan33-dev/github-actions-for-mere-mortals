# Deployments

CI asks "does it work?"; CD asks "is it live?" Deploying from
Actions means the release process is **code**: reviewed,
versioned, and repeatable — not a wiki page and a prayer.

## The Pages pattern (this guide does it)

```yaml
name: Deploy docs
on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: github-pages
    steps:
      - uses: actions/checkout@v4
      - run: ./build-docs.sh
      - uses: actions/upload-pages-artifact@v3
        with: {path: site/}
      - uses: actions/deploy-pages@v4
```

**Check (validated):** push to `main` → docs build → artifact →
live site. `permissions:` grants only what's needed; `environment:
github-pages` ties it to a named environment.

## Environments and approvals

**Environments** (Settings → Environments) are named deployment
targets — `staging`, `production` — each with its own secrets and
rules:

```yaml
jobs:
  deploy-prod:
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://example.com
    steps:
      - run: ./deploy.sh
        env:
          API_KEY: ${{ secrets.PROD_API_KEY }}
```

Add **required reviewers** to `production` and the workflow *pauses*
until a human approves — automation does the work, humans hold the
launch key. This is Continuous *Delivery* (human-gated) vs.
Continuous *Deployment* (fully automatic).

## The release-tag pattern

```yaml
on:
  push:
    tags: ["v*"]

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      - run: ./build.sh
      - uses: softprops/action-gh-release@v2
        with:
          files: dist/*
```

Push `v1.2.3` → build → GitHub Release with binaries attached.
Tagging becomes shipping — one command, full ceremony.

## Deployment rules that prevent incidents

1. **Deploy the artifact you tested.** Build once, promote the
   same bits through staging → production. Rebuilding per
   environment invites "works in staging" mysteries.
2. **Every deploy traceable.** The run page shows who/what/when —
   better than any deployment log spreadsheet.
3. **Rollbacks are a button.** Keep the previous artifact; `git
   revert` + re-tag beats 2 AM heroics.
4. **Production needs a human.** Required reviewers on the
   production environment. Fully automatic prod deploys are for
   teams with the test coverage to deserve them.

**Careful:** a workflow with `contents: write` and a deploy key
can push to your repo — that's the point, and the risk. Review
deployment workflows like you'd review production code, because
they *are* production code.

**Next:** [Debugging](debugging.md)
