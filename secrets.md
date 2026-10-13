# Secrets

API keys, tokens, passwords — workflows need them, and they must
**never** appear in the repo. **Secrets** are GitHub's answer:
encrypted values injected as environment variables at runtime.

## Using a secret

Settings → Secrets and variables → Actions → **New repository
secret**. Name it `PYPI_TOKEN`. Then:

```yaml
steps:
  - run: twine upload dist/*
    env:
      TWINE_PASSWORD: ${{ secrets.PYPI_TOKEN }}
```

**Check (validated):** the secret arrives as an env var; the value
never appears in YAML, logs, or the repo.

## The rules (all of them matter)

1. **Never `echo` a secret.** GitHub masks secrets in logs
   (`***`), but masking is a backstop, not a plan — a secret
   transformed (base64'd, split) won't match the mask.
2. **Never commit a secret.** Not "temporarily," not "I'll remove
   it." Git history remembers; rotate any key that touched a
   commit.
3. **Prefer the automatic token.** Every run gets
   `secrets.GITHUB_TOKEN` — scoped to the repo, expires after the
   run. For pushing commits, commenting on PRs, publishing
   releases: use it, not a personal token.
4. **Least privilege.** Set `permissions:` explicitly:

```yaml
permissions:
  contents: read    # default should be this, not write
```

A compromised action with `contents: write` can push to your repo;
with `read` it can only look. (See [The Marketplace](marketplace.md)
on trusting actions.)

5. **Environment secrets** for deployment targets: staging gets
   one key, production another — attached to **environments**
   (see [Deployments](deployments.md)), optionally with required
   reviewers.

## Pull requests and secrets: the fork problem

Workflows triggered by PRs **from forks** don't get your secrets
(except read-only `GITHUB_TOKEN`) — otherwise anyone could open a
PR printing your keys. Consequence: a PR workflow that deploys or
publishes will fail on forks. That's the design. For fork PRs
needing secrets, a maintainer's `workflow_dispatch` or a labeled
"safe to test" flow is the standard answer.

## Rotating

When someone leaves, when a key leaks, on a schedule: regenerate
the token at the provider, paste the new value into the same
secret name, done — no YAML changes. Name secrets by *purpose*
(`DEPLOY_KEY`), not by person.

**Next:** [Artifacts and Caching](artifacts-caching.md)
