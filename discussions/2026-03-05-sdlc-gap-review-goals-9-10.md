# Discussion: SDLC Gap Review — Defining Goals 9 & 10

**Date:** 2026-03-05
**Participants:** Hussein (Engineering Manager), Peter (Tech Lead)
**Subject:** Phase 0 SDLC coverage audit; identify missing goals; define Goals 9 (Deployment) and 10 (Team Metrics)
**Status:** Resolved — Goals 9 & 10 defined, files created, Phase 0 table updated

---

## Context

After completing Goals 1–8, the team reviewed what Phase 0 actually covers against the full SDLC flow described in the roadmap. Two gaps were identified that had appeared as training items (items 9 and 10) during the earlier Roadmap Gap Review but were never turned into SMART goals with implementation plans. Before Phase 1 begins, both gaps are being closed.

### SDLC Coverage Audit (what Goals 1–8 address)

| SDLC Step | Goal |
|---|---|
| Work Item Creation | Goal 3 — `/create-work-items` skill |
| Implementation Support | Goals 1, 2, 4, 8 — workspace, AGENTS.md, arch docs, skills |
| PR Creation | Goal 6 — `/branch-and-pr` skill |
| Testing | Goal 7 — Playwright MCP |
| Business Documentation | Goal 5 — MCP doc server |
| **Deployment** | **Not covered** |
| **Team Metrics / Visibility** | **Not covered** |

---

## Discussion

### Round 1 — Deployment Automation

**Hussein:** These were flagged as gaps in our roadmap review — Automated Deployment Pipelines was training item 9. Phase 0 closes the SDLC loop only if code actually ships after the PR merges. Right now we have CI (Goal 7) but no CD. What does the TL propose?

**Peter:** I'd scope it to GitHub Actions deployment pipelines for all three Agency repos. `agency-gig-api` deploys the .NET API to a container (Docker + GitHub Container Registry), `agency-gig-web` deploys to Vercel or a container, `agency-gig-ui` publishes to GitHub Packages on version bump. Gate: no merge to main without a green deploy on staging.

**Hussein:** Owner? "DevOps" is too vague.

**Peter:** DevOps Engineer, with TPL as the escalation point.

**Hussein:** Measurable needs to be concrete.

**Peter:** All 3 repos deploy to a staging environment automatically on merge to main, with zero manual steps. Deployment success/failure is visible in the GitHub Actions run. First time all 3 pass end-to-end is the milestone. Ongoing: deployment success rate ≥ 95% per sprint.

**Hussein:** Time-bound?

**Peter:** Week 9. Goal 7 (Playwright) is Week 8 — CD builds directly on that pattern.

**Hussein:** One addition: credential hygiene. All deploy tokens, registry credentials, and environment secrets must be in GitHub Secrets — nothing in YAML literals or committed `.env` files.

**Peter:** Agreed. I'll add a secrets runbook as a deliverable — rotation procedure, rollback procedure, environment variable inventory.

**Hussein:** Branch protection update?

**Peter:** Yes — merge to main is gated on passing `deploy-staging`. Failed deploy blocks the next merge until resolved.

**Hussein:** That closes the loop. Goal 9 agreed.

---

### Round 2 — Team Metrics & Engineering Health Reports

**Hussein:** Training item 10 was AI-Generated Team Metrics and Reports. The EM problem is simple: I have no visibility into whether Phase 0 is working. Goals were implemented — but are deployments actually happening? Are PRs reviewed fast or sitting for three days? Is CI passing or failing half the time?

**Peter:** A weekly automated report pulled from the GitHub API. Metrics: PR cycle time (open → merge), deployment frequency (CD runs per week), CI pass rate, and open issue count. Published as a Markdown report committed to the repo and as a GitHub issue.

**Hussein:** No third-party tooling?

**Peter:** GitHub Actions + GitHub REST API + a Node.js script. No SaaS, no dashboards, no new accounts. Just data from repos we already own.

**Hussein:** What triggers it?

**Peter:** A scheduled GitHub Actions workflow — every Monday at 08:00 UTC. Plus a manual trigger so I can run it any time.

**Hussein:** Owner?

**Peter:** EM is the DRI — you're the consumer of the data. TPL owns the data pipeline and keeps the script accurate.

**Hussein:** What makes it measurable?

**Peter:** Report auto-generates every Monday without anyone touching it. EM can see a 4-week trend for each metric. First milestone: first auto-generated report published within the sprint.

**Hussein:** Dependency on Goal 9?

**Peter:** Yes — the deployment frequency metric only means something once CD pipelines from Goal 9 are live. Week 10 is the right target, one week after Goal 9.

**Hussein:** Thresholds?

**Peter:** PR cycle time ≤ 48h (median), CI pass rate ≥ 90%, deployment frequency ≥ 1 per week per repo. Anything breaching threshold gets flagged in the report with a visual indicator. Runbook documents how to interpret and respond.

**Hussein:** How do I know the data is trustworthy?

**Peter:** The script exits non-zero on API failure — GitHub Actions will show a red run. We'll also include a sample hand-crafted reference report so the EM can spot-check the output format on the first real run.

**Hussein:** Goal 10 agreed.

---

## Decisions Made

| # | Decision |
|---|---|
| 1 | Goal 9 (Deployment Automation) is added to Phase 0 — GitHub Actions CD for all 3 repos |
| 2 | Goal 9 owner: DevOps Engineer (DRI), TPL (escalation) |
| 3 | Goal 9 target: Week 9 — immediately after Goal 7 (Playwright CI, Week 8) |
| 4 | All deploy secrets in GitHub Secrets; deployment runbook is a required deliverable |
| 5 | Branch protection gates merge on passing `deploy-staging` job |
| 6 | Goal 10 (Team Metrics) is added to Phase 0 — weekly GitHub API report |
| 7 | Goal 10 owner: EM (DRI, consumer), TPL (data pipeline owner) |
| 8 | Goal 10 target: Week 10 — depends on Goal 9 for deployment frequency data |
| 9 | No third-party SaaS — GitHub Actions + GitHub API + Node.js only |
| 10 | Report committed to `docs/metrics/` and posted as a GitHub issue labelled `metrics` |
| 11 | Phase 2–4 goals renumbered 11–16 to accommodate the new Phase 0 goals |

---

## Outcome

- `goal-09-deployment-automation.md` and `goal-09-deployment-automation/implementation-plan.md` created
- `goal-10-team-metrics-reports.md` and `goal-10-team-metrics-reports/implementation-plan.md` created
- `ai-roadmap/README.md` Phase 0 table updated; Phase 2–4 goals renumbered 11–16
- `progress.md` updated with rows 9 & 10 as Pending; Phase 2–4 renumbered
- Committed and pushed to `agency-workspace` main
