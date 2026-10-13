# Debugging

Red X. Now what? Reading a failed run is a skill — here's the
method.

## Reading the logs

1. Actions tab → the failed run → the failed job (red).
2. Expand the failed **step** — the error is usually in the last
   20 lines. Scroll *up* from the bottom; the actual cause hides
   above the cascade.
3. Steps are collapsed by default; the one with the red mark is
   where it broke — but the *cause* may be an earlier step's
   warning.

**Enable debug logging** when the logs aren't enough — re-run with
**"Enable debug logging"** checked, or set the secret
`ACTIONS_STEP_DEBUG=true`. Verbose, but it shows what the runner
actually executed.

## Reproducing locally

The runner is Ubuntu with your steps — so run your steps:

```bash
# what the runner does, roughly:
git clone <repo> && cd <repo>
pip install -r requirements.txt
pytest
```

If it fails locally too, it's your code, not Actions. If it
passes locally but fails in CI: environment difference — versions
(`python --version` in both places), missing system packages
(`apt-get` in CI what you installed months ago locally), or
**case sensitivity** (macOS/Windows forgive `Import MyModule`;
Linux does not — the classic "passes here, fails there").

## act: Actions on your laptop

[`act`](https://github.com/nektos/act) runs workflows locally in
Docker — fast iteration without pushing:

```bash
act push        # simulate a push event
act -j test     # just the test job
```

**Check:** `act --list` shows your workflow's jobs without running
anything. Caveat: it's an approximation (Docker ≠ GitHub's VMs),
but it catches YAML errors and logic bugs in seconds instead of
minutes.

## Conditional debugging steps

```yaml
- name: Dump context on failure
  if: failure()
  run: |
    echo "=== disk ===" && df -h
    echo "=== env ===" && env | sort
```

`if: failure()` steps only run when something broke — a flight
recorder for your workflow. (Careful: `env | sort` near secrets —
GitHub masks known secrets, but keep debug dumps away from
production.)

## The usual suspects (quick triage)

| Symptom | Likely cause |
|---------|--------------|
| "command not found" | Tool not installed on runner; add a setup step |
| Works locally, fails in CI | Version drift or case-sensitivity |
| Fails only on Windows | Path separators, line endings, shell differences |
| Hangs forever | Waiting on input — CI has no keyboard; add `--yes`/`--non-interactive` |
| "No such file" | Wrong working directory, or artifact not downloaded |

**Next:** [Troubleshooting](troubleshooting.md)
