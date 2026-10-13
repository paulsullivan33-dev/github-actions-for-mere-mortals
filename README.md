# GitHub Actions for Mere Mortals

Every time you push code, someone should run the tests, check the
style, and build the docs — before the bug reaches production.
**GitHub Actions** is that someone: automation that lives in your
repo and runs on GitHub's computers whenever something happens.

No DevOps degree required. Ideas first, YAML second.

## What this guide covers

- **What CI/CD is** — and why "it works on my machine" isn't a
  deployment strategy
- **Your first workflow** — a real one, in 20 lines
- **Triggers** — what starts a workflow (push, PR, schedule, manual)
- **Jobs, steps, and runners** — the anatomy of a workflow
- **The Marketplace** — borrowing other people's automation safely
- **Matrix builds** — testing every Python/OS combo at once
- **Secrets** — passwords and tokens without committing them
- **Artifacts and caching** — keeping build outputs, skipping
  repeated work
- **Pull request workflows** — CI as a gatekeeper
- **Deployments** — shipping to Pages and beyond, with approvals
- **Debugging** — reading logs like a detective
- **Troubleshooting** — the failures you'll actually hit

## What it doesn't cover

Self-hosted runners in depth, writing custom JavaScript actions
from scratch, or enterprise GitHub Server administration. This is
the 90% use case: hosted runners, YAML workflows, shipping
software.

## How to use this guide

Every YAML example was parsed and validated before shipping — the
syntax is real. **Check:** blocks confirm what you should see.
**Careful:** marks anything involving secrets, deployments, or
spending CI minutes. A free GitHub account includes generous
Actions minutes; private repos on the free plan get 2,000
minutes/month.

You need a GitHub repo to follow along hands-on — any repo works,
even an empty one.

## Chapters

1. [What CI/CD Is](what-is-ci.md)
2. [Your First Workflow](your-first-workflow.md)
3. [Triggers](triggers.md)
4. [Jobs, Steps, and Runners](jobs-steps-runners.md)
5. [The Marketplace](marketplace.md)
6. [Matrix Builds](matrix-builds.md)
7. [Secrets](secrets.md)
8. [Artifacts and Caching](artifacts-caching.md)
9. [Pull Request Workflows](pull-requests.md)
10. [Deployments](deployments.md)
11. [Debugging](debugging.md)
12. [Troubleshooting](troubleshooting.md)
13. [Glossary](glossary.md)

*Written to the [nine-point writing standard](criteria.md) shared by
every guide in this series.*
