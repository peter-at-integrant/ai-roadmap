# Goal 10 (Team Metrics): Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What are the metrics tracked in the weekly report, and why was each one chosen?**

> - **PR cycle time** — measures flow efficiency; long cycle times reveal review bottlenecks.
> - **CI pass rate** — indicates codebase health and test reliability; a declining rate signals instability.
> - **Deployment frequency** — shows how often value reaches users; low frequency suggests process friction.
> - **Open issue age** — highlights work that's stagnating and may need reprioritisation.
> Each was chosen because it's directly actionable — a change in any metric points to a specific process or quality problem the team can address.

---

**Q2. What does "PR cycle time" mean? What's a healthy vs unhealthy value, and what causes it to spike?**

> PR cycle time is the elapsed time from when a PR is opened to when it is merged. A healthy value for a small team is under 24 hours; over 3 days signals a problem. Causes of spikes: PRs are too large (reviewers defer them), reviewers are unavailable or overloaded, the PR needs multiple rounds of back-and-forth, or the branch has conflicts that delay merging. A sustained spike means the team should look at PR size, review norms, or workload distribution.

---

**Q3. How is the report generated and where does it get published?**

> A scheduled GitHub Actions workflow runs weekly (e.g. Monday morning). It calls the GitHub API to collect the raw metrics, passes them to the MCP server or AI skill for formatting, and produces a markdown report. The report is published as a GitHub Actions artifact and also posted to the team's Slack channel (or a designated GitHub Discussion) so it's visible without needing to navigate to the CI run.

---

**Q4. The CI pass rate drops to 60% this week. What does that signal, and what action do you take?**

> A 60% pass rate means 4 in 10 CI runs are failing — this is a serious signal of instability. It could mean: flaky tests that fail non-deterministically, a breaking change that wasn't caught before merging, or an environment/infrastructure issue. Immediate action: identify whether failures are in the same test(s) or spread across the suite. If it's one flaky test, quarantine it (skip + file an issue) to unblock the team. If it's a broken build, treat it as a P1 — stop merging unrelated work until the root cause is fixed. Surface it in the team standup the same day.
