# Goal 10: Automated Team Metrics & Engineering Health Reports

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** Engineering Manager (DRI); TPL (data owner)
**Target:** Week 10

## Description

Close the visibility gap in Phase 0 by automating the collection and reporting of key engineering health metrics. A weekly GitHub Actions workflow pulls data from the GitHub API and produces a structured Markdown report covering PR cycle time, deployment frequency, CI pass rate, and open issue age — published as a GitHub issue and stored as a workflow artifact. No manual data gathering.

## SMART Breakdown

- **Specific:** Build a GitHub Actions workflow that queries the GitHub REST API for the `agency-gig-api`, `agency-gig-web`, and `agency-gig-ui` repos and computes: PR cycle time (open → merge), deployment frequency (CD pipeline runs per week), CI pass rate (passing runs / total runs), and open issue count by label. Output: a formatted Markdown report committed to `docs/metrics/` and posted as a GitHub issue labelled `metrics`.
- **Measurable:** Report auto-generates every Monday. EM can view the last 4-week trend for each metric without opening GitHub manually. First milestone: first auto-generated report published within the sprint. Ongoing: report generated without manual intervention every week.
- **Achievable:** GitHub Actions + GitHub REST API require no new infrastructure. Goal 9 (CD pipelines) produces the deployment events this goal aggregates. A single Node.js/Python script handles the API calls and report generation — no third-party SaaS required.
- **Relevant:** Phase 0 goals produce SDLC automation artefacts but no visibility layer. The EM needs objective data to assess whether automation is delivering value. Engineering metrics are the feedback loop that justifies and guides Phase 1 investment.
- **Time-bound:** Complete by end of Week 10. Goal 9 (Deployment Pipelines) completes Week 9 — metrics can only meaningfully report on CD data once pipelines are live.

## Deliverables

- [ ] `tools/scripts/collect-metrics.js` — Node.js script: GitHub API calls for PRs, runs, issues across 3 repos; outputs JSON
- [ ] `tools/scripts/generate-metrics-report.js` — consumes JSON, renders Markdown report
- [ ] `.github/workflows/metrics-report.yml` — runs every Monday at 08:00 UTC; calls both scripts; commits report to `docs/metrics/YYYY-MM-DD.md`; creates GitHub issue with report body
- [ ] `docs/metrics/` directory with a sample hand-crafted report as reference format
- [ ] `docs/metrics-runbook.md` — how to interpret each metric, thresholds (e.g. PR cycle time > 2 days = flag), and how to re-run manually
- [ ] GitHub Secret `METRICS_TOKEN` (PAT with `repo` + `read:org` scopes) documented in runbook
- [ ] EM walkthrough: first live report reviewed in team meeting

## Acceptance Criteria

- Every Monday a new `docs/metrics/YYYY-MM-DD.md` report appears in `agency-workspace` without manual action.
- The report includes: PR cycle time (median, p90), deployment frequency, CI pass rate, and open issue count — for all three repos.
- A GitHub issue labelled `metrics` is created automatically linking to the report file.
- The script handles API rate limits gracefully (exponential back-off, exits with non-zero on failure).
- All GitHub tokens are stored in GitHub Secrets — not in script files or workflow YAML literals.
