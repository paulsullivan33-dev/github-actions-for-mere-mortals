# Glossary

| Term | Meaning |
|------|---------|
| act | Local Actions runner (Docker) for fast iteration |
| action | Reusable automation unit, borrowed via `uses:` |
| artifact | Files a job saves — downloadable from the run page |
| cache | Stored dependencies reused between runs |
| CD | Continuous Delivery/Deployment — shipping automatically |
| checkout | `actions/checkout` — puts your code on the runner |
| CI | Continuous Integration — automated test/build on every change |
| cron | Timer syntax for scheduled workflows (UTC!) |
| draft PR | Work-in-progress PR — still triggers CI by default |
| environment | Named deploy target (staging/production) with secrets and approvals |
| expression | `${{ }}` template syntax for variables and functions |
| fail-fast | Cancel remaining matrix jobs when one fails (default true) |
| fork PR | PR from a fork — gets no secrets by design |
| GITHUB_TOKEN | Automatic per-run token, scoped to the repo |
| job | Work unit running on one fresh VM; parallel by default |
| Marketplace | GitHub's catalog of reusable actions |
| matrix | Job multiplier — one definition, many combinations |
| merge hell | The pain of integrating rarely — CI's enemy |
| needs | Job dependency — "wait for this job, skip if it fails" |
| permissions | Explicit token scopes — least privilege |
| runner | The VM (or machine) executing a job |
| secret | Encrypted value injected at runtime, never in the repo |
| self-hosted runner | Your own machine as a runner |
| status check | CI result gating a merge (the green check) |
| step | One command/action inside a job; sequential |
| trigger (`on:`) | The event starting a workflow |
| workflow | One automation recipe (a YAML file in `.github/workflows/`) |
| workflow_dispatch | Manual trigger — the "Run workflow" button |
