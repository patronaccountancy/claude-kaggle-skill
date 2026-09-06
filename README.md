# claude-kaggle-skill

A Claude Code skill that makes Claude act as a **Kaggle Grandmaster / 20+ year data scientist** —
and, more importantly, keeps it honest about whether anything it did actually helped.

Most of an agent's Kaggle output is not wrong because it cannot fit a model. It is wrong
because it believes its own cross-validation score. This skill is mostly a set of rules
against that.

## What it does

- **The one law** — a local CV gain is a hypothesis, the leaderboard is the verdict. It asks before it submits, and records CV delta vs LB delta every time.
- **Loop** — discover → research → hypothesise → experiment → analyse failures → submit → learn. Baseline on the board day one, so CV is calibrated against LB before anything is built on it.
- **Validation rules** — match CV to the test split, never tune on the seeds you report, never A/B against a moving baseline, establish the noise floor first, fit every transform inside the fold.
- **Test everything** — shuffled-target leakage test, reproducibility, fold hygiene, train/serve parity, submission shape. One `assert`-based file, no framework.
- **Simulate** — reimplement the competition metric locally, bootstrap the public/private split, simulate the shakeup before picking final submissions, rebuild the data-generating process where one exists.
- **Invent** — five ranked sources of genuinely new ideas, batch-ranked by expected gain × confidence ÷ cost, killed cheaply, logged so nothing is rediscovered.
- **Get better at getting better** — a stall ladder (change the level, not the parameters) and budget reallocation from the experiment log.

## `/kaggle init`

Joining a project where work already happened — a notebook, a folder of submissions, a long
chat — before the skill was loaded? `/kaggle init` adopts it: inventories what exists,
rebuilds the experiment ledger from the real submission history, re-runs the current best to
check it reproduces, runs the leakage and fold-hygiene tests against the existing code,
reconciles claimed CV against actual LB, then marks every inherited claim VERIFIED /
UNVERIFIED / REFUTED. Only verified numbers become a baseline. It writes `STATE.md` and
`experiments.md` so nothing is lost when the context window ends.

Expect the honest score to come out **lower** than the one you had been reading. That drop is
the point — it was never real, and the leaderboard was going to collect the difference anyway.

## Install

As a plugin:

```
/plugin marketplace add patronaccountancy/claude-kaggle-skill
/plugin install kaggle@patron-kaggle
```

Or as a plain skill, which keeps the invocation `/kaggle` instead of `/kaggle:kaggle`:

```bash
git clone https://github.com/patronaccountancy/claude-kaggle-skill.git /tmp/cks
cp -r /tmp/cks/kaggle/skills/kaggle ~/.claude/skills/kaggle
```

Restart Claude Code. Invoke it explicitly, or let it trigger itself on any modelling,
dataset or competition work.

## Requires

`kaggle` CLI on PATH with `~/.kaggle/kaggle.json` configured, for downloads and submissions.
