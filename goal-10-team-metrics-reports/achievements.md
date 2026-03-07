# Achievements — Goal 10: Team Metrics & Engineering Health Reports

**Status:** 🔜 Pending
**Goal file:** [goal-10-team-metrics-reports.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

_Not yet implemented. This file will be updated when the goal is complete._

## What will be built

| File | Repo | Purpose |
|---|---|---|
| [`tools/scripts/collect-metrics.js`](../../tools/scripts/collect-metrics.js) | `agency-workspace` | GitHub API data collection — PRs, CI runs, issues across 3 repos |
| [`tools/scripts/generate-metrics-report.js`](../../tools/scripts/generate-metrics-report.js) | `agency-workspace` | Converts JSON snapshot into Markdown report |
| [`.github/workflows/metrics-report.yml`](../../.github/workflows/metrics-report.yml) | `agency-workspace` | Runs every Monday 08:00 UTC; commits report; opens GitHub issue |
| `docs/metrics/YYYY-MM-DD.md` | `agency-workspace` | Weekly report — PR cycle time, deployment frequency, CI pass rate, open issues |
| [`docs/metrics-runbook.md`](../../docs/metrics-runbook.md) | `agency-workspace` | Metric definitions, thresholds, and interpretation guide |

## How to verify (once implemented)

Trigger the workflow manually: Actions → `Weekly Engineering Metrics Report` → `Run workflow`.

A new `docs/metrics/YYYY-MM-DD.md` report should be committed and a GitHub issue labelled `metrics` should appear in [agency-workspace Issues](https://github.com/peter-at-integrant/agency-workspace/issues).
