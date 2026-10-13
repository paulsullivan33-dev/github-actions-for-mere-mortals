# Matrix Builds

"Does it work on Python 3.11 *and* 3.12 *and* 3.13, on Linux *and*
Windows?" A **matrix** answers by multiplying one job definition
into many — no copy-paste.

## The basic matrix

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.11", "3.12", "3.13"]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - run: pytest
```

**Check (validated):** three jobs spawn — one per Python version —
each with `matrix.python-version` substituted in. One definition,
three VMs, zero duplication.

## Two dimensions

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    python-version: ["3.12", "3.13"]
```

Four jobs: every combination (2 × 2). Reference both:

```yaml
runs-on: ${{ matrix.os }}
```

**Careful:** dimensions multiply. Three OSes × four Pythons × two
architectures = 24 jobs. That's the point — but watch your minutes
on private repos.

## Fail-fast and friends

```yaml
strategy:
  fail-fast: false
  matrix:
    python-version: ["3.11", "3.12", "3.13"]
```

Default `fail-fast: true` cancels the other jobs when one fails —
fast signal, but you don't learn whether 3.13 *also* fails. Set
`false` when you want the full picture (typical for
version-compatibility matrices).

## Excluding combinations

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    python-version: ["3.12", "3.13"]
    exclude:
      - os: windows-latest
        python-version: "3.12"
```

For the combos you know don't apply (or can't work). There's also
`include:` for one-off additions — like a job with an extra
experimental flag.

## Naming the jobs

```yaml
name: test (${{ matrix.os }}, py${{ matrix.python-version }})
```

Without this, the UI shows "test (1)", "test (2)" — inscrutable.
Name them; future-you debugging a red Windows-only failure will be
grateful.

**Next:** [Secrets](secrets.md)
