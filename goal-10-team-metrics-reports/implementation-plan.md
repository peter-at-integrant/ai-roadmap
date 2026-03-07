# Implementation Plan — Goal 10: Automated Team Metrics & Engineering Health Reports

**Goal:** Auto-generate weekly engineering health reports from GitHub API data — PR cycle time, deployment frequency, CI pass rate, open issue age — with zero manual data gathering.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** Engineering Manager
**Target:** Week 10

---

## Approach

A Node.js script (`collect-metrics.js`) queries the GitHub REST API for all three Agency repos and writes a JSON snapshot. A second script (`generate-metrics-report.js`) reads the JSON and renders a Markdown report. A GitHub Actions workflow runs both scripts every Monday at 08:00 UTC, commits the report to `docs/metrics/`, and opens a GitHub issue with the report body. No third-party SaaS; only `GITHUB_TOKEN` (or a PAT with `repo` scope) needed.

---

## Step-by-Step Implementation

### Step 1 — Repository structure

In `agency-workspace`:

```
agency-workspace/
  tools/
    scripts/
      collect-metrics.js       ← GitHub API data collection
      generate-metrics-report.js ← JSON → Markdown report
  docs/
    metrics/
      README.md                ← metric definitions and thresholds
      2026-03-10.md            ← sample hand-crafted reference report
  .github/
    workflows/
      metrics-report.yml
```

### Step 2 — Data collection script

Create `tools/scripts/collect-metrics.js`:

```javascript
#!/usr/bin/env node
// Collects engineering metrics from GitHub API for all three Agency repos.
// Outputs: docs/metrics/data-<YYYY-MM-DD>.json

const https = require("https");
const fs    = require("fs");
const path  = require("path");

const REPOS  = ["agency-gig-api", "agency-gig-web", "agency-gig-ui"];
const ORG    = "peter-at-integrant";
const TOKEN  = process.env.METRICS_TOKEN;
const TODAY  = new Date().toISOString().slice(0, 10);
const SINCE  = new Date(Date.now() - 7 * 24 * 60 * 60 * 1000).toISOString();

function ghFetch(path) {
  return new Promise((resolve, reject) => {
    const options = {
      hostname: "api.github.com",
      path,
      headers: {
        "Authorization": `Bearer ${TOKEN}`,
        "User-Agent": "agency-metrics-bot",
        "Accept": "application/vnd.github+json",
      },
    };
    https.get(options, res => {
      let body = "";
      res.on("data", chunk => body += chunk);
      res.on("end", () => resolve(JSON.parse(body)));
    }).on("error", reject);
  });
}

async function collectRepo(repo) {
  const [prs, runs, issues] = await Promise.all([
    ghFetch(`/repos/${ORG}/${repo}/pulls?state=closed&per_page=100&sort=updated`),
    ghFetch(`/repos/${ORG}/${repo}/actions/runs?per_page=50`),
    ghFetch(`/repos/${ORG}/${repo}/issues?state=open&per_page=100`),
  ]);

  // PR cycle time: merged in last 7 days
  const mergedPRs = prs.filter(p => p.merged_at && p.merged_at >= SINCE);
  const cycleTimes = mergedPRs.map(p =>
    (new Date(p.merged_at) - new Date(p.created_at)) / 3600000
  );

  const recentRuns = (runs.workflow_runs || []).filter(r => r.created_at >= SINCE);
  const passed     = recentRuns.filter(r => r.conclusion === "success").length;

  return {
    repo,
    prCycleTime: {
      count:  cycleTimes.length,
      median: median(cycleTimes),
      p90:    percentile(cycleTimes, 90),
    },
    deploymentFrequency: recentRuns.filter(r =>
      r.name?.toLowerCase().includes("cd") && r.conclusion === "success"
    ).length,
    ciPassRate: recentRuns.length ? Math.round((passed / recentRuns.length) * 100) : null,
    openIssues: (issues || []).length,
  };
}

function median(arr) {
  if (!arr.length) return null;
  const s = [...arr].sort((a, b) => a - b);
  const m = Math.floor(s.length / 2);
  return Math.round(s.length % 2 ? s[m] : (s[m - 1] + s[m]) / 2);
}

function percentile(arr, p) {
  if (!arr.length) return null;
  const s = [...arr].sort((a, b) => a - b);
  return Math.round(s[Math.floor((p / 100) * s.length)]);
}

(async () => {
  const results = await Promise.all(REPOS.map(collectRepo));
  const out = path.join(__dirname, "../../docs/metrics", `data-${TODAY}.json`);
  fs.mkdirSync(path.dirname(out), { recursive: true });
  fs.writeFileSync(out, JSON.stringify({ date: TODAY, repos: results }, null, 2));
  console.log(`Metrics written to ${out}`);
})();
```

### Step 3 — Report generation script

Create `tools/scripts/generate-metrics-report.js`:

```javascript
#!/usr/bin/env node
// Reads JSON snapshot and renders a Markdown engineering health report.

const fs   = require("fs");
const path = require("path");

const TODAY   = new Date().toISOString().slice(0, 10);
const dataFile = path.join(__dirname, "../../docs/metrics", `data-${TODAY}.json`);
const { repos } = JSON.parse(fs.readFileSync(dataFile, "utf-8"));

function flag(value, threshold, unit = "h") {
  if (value === null) return "—";
  return value > threshold ? `**${value}${unit} ⚠️**` : `${value}${unit}`;
}

const lines = [
  `# Engineering Health Report — ${TODAY}`,
  "",
  "_Auto-generated by [metrics-report workflow](../.github/workflows/metrics-report.yml). Period: last 7 days._",
  "",
  "## Summary",
  "",
  "| Repo | PR Cycle Time (median) | PR Cycle Time (p90) | Deployments | CI Pass Rate | Open Issues |",
  "|---|---|---|---|---|---|",
  ...repos.map(r => [
    `| \`${r.repo}\``,
    flag(r.prCycleTime.median, 48),
    flag(r.prCycleTime.p90, 72),
    `${r.deploymentFrequency}`,
    r.ciPassRate !== null ? `${r.ciPassRate}%` : "—",
    `${r.openIssues}`,
  ].join(" | ") + " |"),
  "",
  "## Thresholds",
  "",
  "| Metric | Target | Flag |",
  "|---|---|---|",
  "| PR Cycle Time (median) | ≤ 48h | > 48h ⚠️ |",
  "| PR Cycle Time (p90) | ≤ 72h | > 72h ⚠️ |",
  "| Deployment Frequency | ≥ 1/week per repo | 0 ⚠️ |",
  "| CI Pass Rate | ≥ 90% | < 90% ⚠️ |",
  "",
  "---",
  "_See `docs/metrics-runbook.md` for interpretation guidance._",
];

const reportPath = path.join(__dirname, "../../docs/metrics", `${TODAY}.md`);
fs.writeFileSync(reportPath, lines.join("\n"));
console.log(`Report written to ${reportPath}`);
```

### Step 4 — GitHub Actions workflow

Create `.github/workflows/metrics-report.yml` in `agency-workspace`:

```yaml
name: Weekly Engineering Metrics Report

on:
  schedule:
    - cron: '0 8 * * 1'   # Every Monday at 08:00 UTC
  workflow_dispatch:        # Allow manual trigger

jobs:
  generate-report:
    runs-on: ubuntu-latest
    permissions:
      contents: write
      issues: write

    steps:
      - uses: actions/checkout@v4
        with:
          token: ${{ secrets.METRICS_TOKEN }}

      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Collect metrics
        run: node tools/scripts/collect-metrics.js
        env:
          METRICS_TOKEN: ${{ secrets.METRICS_TOKEN }}

      - name: Generate report
        run: node tools/scripts/generate-metrics-report.js

      - name: Commit report
        run: |
          git config user.name  "agency-metrics-bot"
          git config user.email "metrics-bot@peter-at-integrant"
          git add docs/metrics/
          git commit -m "metrics: weekly report $(date +%Y-%m-%d)" || echo "No changes"
          git push

      - name: Create GitHub issue
        uses: peter-evans/create-issue-from-file@v5
        with:
          title: "Engineering Health Report — ${{ env.REPORT_DATE }}"
          content-filepath: docs/metrics/${{ env.REPORT_DATE }}.md
          labels: metrics
        env:
          GITHUB_TOKEN: ${{ secrets.METRICS_TOKEN }}
          REPORT_DATE: ${{ env.REPORT_DATE }}
```

### Step 5 — Sample reference report

Create `docs/metrics/README.md`:

```markdown
# Engineering Metrics

Reports are auto-generated every Monday and committed here as `YYYY-MM-DD.md`.
A GitHub issue labelled `metrics` is also created for each report.

## Metric Definitions

| Metric | Definition |
|---|---|
| PR Cycle Time | Time from PR `created_at` to `merged_at` (hours) |
| Deployment Frequency | CD pipeline successful runs in the last 7 days |
| CI Pass Rate | (successful runs / total runs) × 100 for last 7 days |
| Open Issues | Count of open GitHub issues (all labels) |

## Runbook
See [`docs/metrics-runbook.md`](../metrics-runbook.md).
```

### Step 6 — Metrics runbook

Create `docs/metrics-runbook.md`:

```markdown
# Metrics Runbook

## Interpreting the Report

| Metric | Green | Amber | Red |
|---|---|---|---|
| PR Cycle Time (median) | ≤ 24h | 24–48h | > 48h |
| Deployment Frequency | ≥ 3/week | 1–2/week | 0 |
| CI Pass Rate | ≥ 95% | 90–95% | < 90% |
| Open Issues | Trending down | Flat | Trending up |

## Re-running Manually
GitHub Actions → `Weekly Engineering Metrics Report` → `Run workflow`

## METRICS_TOKEN Setup
1. Create a PAT at github.com → Settings → Developer Settings → Personal Access Tokens
2. Scopes required: `repo`, `read:org`
3. Store as a repository secret: `gh secret set METRICS_TOKEN --repo peter-at-integrant/agency-workspace`
4. Rotate every 90 days; update the secret in place.
```

### Step 7 — Store the secret

```bash
gh secret set METRICS_TOKEN --repo peter-at-integrant/agency-workspace
```

### Step 8 — EM walkthrough

30-minute session:
1. Trigger workflow manually: Actions → `Run workflow`
2. Show the generated `docs/metrics/YYYY-MM-DD.md` report committed to main
3. Open the auto-created GitHub issue — point out the ⚠️ flags
4. Walk through the threshold table in the runbook
5. Agree with team on response protocol when a metric goes red

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Node.js (built-in `https`) | GitHub API calls — no external dependencies |
| GitHub Actions (`schedule`) | Weekly trigger |
| `peter-evans/create-issue-from-file` | Auto-create GitHub issue from report |
| GitHub Secrets (`METRICS_TOKEN`) | PAT with repo + read:org scope |
| `docs/metrics/` | Report archive in repo |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- GitHub REST API usage: pagination, filtering by date, multi-repo aggregation
- Statistical metric computation (median, p90) in plain Node.js
- GitHub Actions `schedule` trigger and `workflow_dispatch` for manual runs
- Automated git commit within a workflow
- EM-facing report design: thresholds, visual flags, runbook

---

## Demo Scenario

> It's Monday 08:00. No one is online yet.
> GitHub Actions triggers automatically. Three API calls later, a new report appears in `docs/metrics/2026-03-10.md`.
> A GitHub issue titled "Engineering Health Report — 2026-03-10" is created.
> In the 09:00 standup the EM opens the issue — PR cycle time for `agency-gig-api` shows ⚠️ 61h.
> Team discusses: two large PRs sat unreviewed over the weekend. Action: pair-review policy for PRs open > 24h.

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| GitHub API rate limits (60 req/h unauth) | Use authenticated PAT — 5,000 req/h |
| PAT expiry causes silent failure | Workflow exits non-zero; branch protection surfaces it; rotate on schedule |
| Noisy issues if metrics are unchanged | Add a diff check — only flag if a metric crosses a threshold |
| Metrics misinterpreted without context | Runbook + EM walkthrough during launch sprint |
