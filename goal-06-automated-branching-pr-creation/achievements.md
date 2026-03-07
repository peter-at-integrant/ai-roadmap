# Achievements — Goal 6: Automated Branching and PR Creation

**Status:** ✅ Done
**Goal file:** [goal-06-automated-branching-pr-creation.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

## What was built

| File | Repo | Purpose |
|---|---|---|
| [`.claude/skills/branch-and-pr.md`](../../.claude/skills/branch-and-pr.md) | `agency-workspace` | Claude Code skill — creates branch and PR from a work item description |
| [`tools/scripts/branch-and-pr.sh`](../../tools/scripts/branch-and-pr.sh) | `agency-workspace` | Bash script — GitHub API: creates branch + opens PR with description |
| [`tools/templates/pr-description.md`](../../tools/templates/pr-description.md) | `agency-workspace` | PR description template (Summary, Changes, Test Plan, Linked Items) |
| [`tools/templates/git-conventions.md`](../../tools/templates/git-conventions.md) | `agency-workspace` | Branch naming and commit message conventions |
| `.claude/branch-pr-config.json` | [agency-gig-api](https://github.com/peter-at-integrant/agency-gig-api/blob/main/.claude/branch-pr-config.json) | Per-repo config: base branch, label defaults, reviewer list |
| `.claude/branch-pr-config.json` | [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web/blob/main/.claude/branch-pr-config.json) | Per-repo config |
| `.claude/branch-pr-config.json` | [agency-gig-ui](https://github.com/peter-at-integrant/agency-gig-ui/blob/main/.claude/branch-pr-config.json) | Per-repo config |

## How to use

**Via Claude Code skill:**
```
/branch-and-pr "Add POST /bookings endpoint for band to request a gig slot" --issue 42
```

**Via shell script directly:**
```bash
export GH_PAT=<your-pat>
bash tools/scripts/branch-and-pr.sh \
  --repo agency-gig-api \
  --issue 42 \
  --title "Add POST /bookings endpoint" \
  --branch "feature/42-add-post-bookings-endpoint"
```

## How to verify

Run the script or skill against `agency-gig-api`. Check [agency-gig-api pulls](https://github.com/peter-at-integrant/agency-gig-api/pulls) — a new PR should appear with a populated description matching the template and linked to the issue number.
