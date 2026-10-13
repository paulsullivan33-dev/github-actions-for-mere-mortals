# What CI/CD Is

**The one-sentence version:** CI/CD is making the computer do the
boring verification work every time code changes — so humans do
the thinking and machines do the checking.

## The two halves

- **CI — Continuous Integration.** Every push automatically runs
  the tests, the linter, the build. Break something? You know in
  minutes, while the change is still fresh in your head — not three
  weeks later when nobody remembers what that commit did.
- **CD — Continuous Delivery/Deployment.** Every passing change is
  packaged and (optionally automatically) shipped. Delivery =
  ready to ship with one click; Deployment = ships itself.

## The problem it solves

Without CI, integration happens "later" — which means rarely,
painfully, and all at once ("merge hell": twenty branches, all
conflicting, nobody's code works together). With CI, integration
happens *continuously*, in small doses. Small doses are painless;
that's the whole trick.

## What GitHub Actions is (and isn't)

GitHub Actions is CI/CD **built into GitHub**: you describe
automation in YAML files inside `.github/workflows/`, and GitHub
runs them on its own computers ("runners") when events happen in
your repo.

- **It is** a general automation engine — CI, docs builds,
  scheduled chores, auto-labeling PRs, anything scriptable.
- **It isn't** a separate service to sign up for, or a server to
  maintain. The runners are GitHub's; you just write the recipe.
- **It isn't** Jenkins. (Jenkins is the self-hosted ancestor —
  powerful, but you feed and water the servers yourself.)

## The vocabulary (you'll see these everywhere)

| Term | Meaning |
|------|---------|
| Workflow | One automation recipe (a YAML file) |
| Trigger | The event that starts it (a push, a schedule...) |
| Job | A unit of work that runs on one machine |
| Step | One command (or borrowed action) inside a job |
| Runner | The computer executing a job |
| Artifact | Files a job saves for later (build outputs, reports) |

That's the whole zoo. Everything else in this guide is details on
these six.

**Next:** [Your First Workflow](your-first-workflow.md)
