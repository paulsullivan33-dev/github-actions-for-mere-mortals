# The Marketplace

`uses:` is the superpower: instead of scripting "install Python"
yourself, you borrow `actions/setup-python@v5` — maintained,
tested, and one line. The Marketplace holds thousands of these
reusable **actions**.

## Using an action

```yaml
steps:
  - uses: actions/checkout@v4      # get the repo's code
  - uses: actions/setup-node@v4    # install Node.js
    with:
      node-version: 20
  - uses: actions/cache@v4         # cache dependencies
    with:
      path: ~/.npm
      key: npm-${{ hashFiles('package-lock.json') }}
```

**Check (validated):** three borrowed actions, each with its
inputs under `with:`. Note `${{ }}` — **expressions**, Actions'
template syntax for variables and functions (see [Secrets](secrets.md)
for the important one).

## The version after the @ matters

`@v4` means "the v4 major release" — a moving tag the maintainer
updates. Options, safest first:

| Pin | Example | Trade-off |
|-----|---------|-----------|
| Full SHA | `actions/checkout@11bd719...` | Immutable; verbose |
| Major tag | `actions/checkout@v4` | Gets patches; could move |
| Branch | `actions/checkout@main` | Living dangerously |

**For anything important: pin the SHA** (GitHub's UI shows it;
Dependabot can update it). A compromised or yanked tag has broken
real pipelines — the SHA can't be rewritten under you.

## Trust, but verify

An action runs **arbitrary code on your runner with your secrets
in reach**. Before `uses:`-ing something:

1. **Who wrote it?** `actions/*` is GitHub-official. A random
   account with 3 stars deserves a look at the source.
2. **What does it do?** Actions are repos — read the README and
   skim `action.yml`.
3. **What permissions does it need?** Least privilege (see
   [Deployments](deployments.md) on `permissions:`).

The Marketplace page shows stars, contributors, and linked repo —
use them like you'd use reviews.

## The essential dozen

| Action | Does |
|--------|------|
| `actions/checkout` | Checks out your code (nearly every workflow) |
| `actions/setup-python` / `setup-node` / `setup-go` / `setup-java` | Installs a toolchain |
| `actions/cache` | Caches dependencies between runs |
| `actions/upload-artifact` / `download-artifact` | Passes files between jobs |
| `actions/github-script` | Runs inline JavaScript against the GitHub API |
| `docker/build-push-action` | Builds and pushes Docker images |
| `pypa/gh-action-pypi-publish` | Publishes to PyPI (trusted publishing) |

You can go surprisingly far on just the `actions/*` official set.

**Next:** [Matrix Builds](matrix-builds.md)
