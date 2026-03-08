# Implementation Plan — Goal 6: Automated Branching and PR Creation

**Goal:** AI-driven workflow that creates correctly named branches and fully populated PRs in any Agency repo from a GitHub issue reference.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** Dev
**Target:** Week 6

---

## Approach

Create a **Claude Code skill** (`/branch-and-pr`) that takes a GitHub issue number, fetches the issue from the `peter-at-integrant` org, generates the branch name per convention, creates and pushes the branch, then opens a PR via the GitHub REST API with a populated description template, linked issue, and assigned reviewer — across any of the three Agency repos.

---

## Step-by-Step Implementation

### Step 1 — Agree branch naming convention

Document in each repo's `AGENTS.md` and `docs/git-conventions.md`:

```
Format:  <type>/<work-item-id>-<short-description>

Types:
  feat/     New feature or user story
  fix/      Bug fix
  chore/    Maintenance, tooling, dependency updates
  docs/     Documentation only
  refactor/ Code restructuring without behaviour change

Examples (Agency):
  feat/1042-band-registration-endpoint
  fix/1089-booking-conflict-not-detected
  chore/1102-upgrade-efcore-9
  feat/1055-venue-dashboard-slot-calendar
```

### Step 2 — PR description template

Create `tools/templates/pr-description.md` in `agency-workspace`:

```markdown
## Summary
<!-- What does this PR do? Which Agency feature does it implement/fix? -->

## Changes
<!-- Bullet list of key changes -->
-

## Test Plan
- [ ] Unit tests added/updated
- [ ] Integration tests pass (`dotnet test` / `npm test`)
- [ ] E2E tests pass (Playwright) — if UI change
- [ ] Manual test: [describe scenario, e.g. "Created a booking request as a band, confirmed as venue"]
- [ ] No regressions in booking/review flows

## Linked Items
<!-- Auto-populated -->

## Screenshots
<!-- For agency-gig-web or agency-gig-ui changes -->

## Notes for Reviewers
<!-- Anything to call out: tricky logic, performance considerations, etc. -->
```

### Step 3 — Per-repo config file

Create `.claude/branch-pr-config.json` in each Agency repo:

`agency-gig-api`:
```json
{
  "githubOrg": "peter-at-integrant",
  "githubRepo": "agency-gig-api",
  "repo": "agency-gig-api",
  "defaultBranch": "main",
  "defaultReviewers": ["peter-at-integrant"],
  "issueTypes": {
    "user-story": "feat",
    "feature":    "feat",
    "bug":        "fix",
    "task":       "chore"
  }
}
```

Same structure for `agency-gig-web` (use `"githubRepo": "agency-gig-web"`) and `agency-gig-ui` (use `"githubRepo": "agency-gig-ui"`).

### Step 4 — Claude Code skill

Create `.claude/skills/branch-and-pr/SKILL.md` in `agency-workspace`:

> **Structure note:** Skills must be a directory containing a `SKILL.md` file with YAML frontmatter (`name` + `description`). A flat `.md` file will not be registered as a slash command.

```markdown
---
name: branch-and-pr
description: This skill should be used when the user wants to create a branch and open a pull request from a GitHub issue number in an Agency repo.
---

# Skill: Branch and PR Creation

## Usage
> /branch-and-pr <issue-number>

## What I will do
1. Read `.claude/branch-pr-config.json` in the current repo
2. Fetch issue #<number> from the GitHub repo
3. Generate branch name: `<type>/<number>-<slugified-title>`
4. Create and push the branch to origin
5. Open a PR with:
   - Title: `[#<number>] <issue title>`
   - Description: template pre-filled with issue acceptance criteria
   - Linked issue: #<number>
   - Reviewer: from config

## Branch type is inferred from issue label using the config map.
```

### Step 5 — Automation script

Create `tools/scripts/branch-and-pr.sh`:

```bash
#!/bin/bash
set -e

ISSUE_NUMBER="$1"
CONFIG=".claude/branch-pr-config.json"
GH_PAT="${GH_PAT}"
ORG=$(jq -r '.githubOrg' "$CONFIG")
REPO=$(jq -r '.githubRepo' "$CONFIG")
DEFAULT_BRANCH=$(jq -r '.defaultBranch' "$CONFIG")

# 1. Fetch issue from GitHub
ISSUE=$(curl -s -H "Authorization: token ${GH_PAT}" \
  "https://api.github.com/repos/${ORG}/${REPO}/issues/${ISSUE_NUMBER}")

ISSUE_TITLE=$(echo "$ISSUE" | jq -r '.title')
ISSUE_LABEL=$(echo "$ISSUE" | jq -r '.labels[0].name // "task"')
ISSUE_BODY=$(echo  "$ISSUE" | jq -r '.body // ""')

# 2. Determine branch prefix from config
PREFIX=$(jq -r --arg t "$ISSUE_LABEL" '.issueTypes[$t] // "feat"' "$CONFIG")

# 3. Slugify title
SLUG=$(echo "$ISSUE_TITLE" \
  | tr '[:upper:]' '[:lower:]' \
  | sed 's/[^a-z0-9]/-/g' \
  | sed 's/--*/-/g' \
  | cut -c1-50 \
  | sed 's/-$//')
BRANCH="${PREFIX}/${ISSUE_NUMBER}-${SLUG}"

# 4. Create and push branch
git checkout -b "$BRANCH"
git push -u origin "$BRANCH"
echo "✓ Branch created: $BRANCH"

# 5. Build PR description
PR_BODY=$(cat tools/templates/pr-description.md \
  | sed "s|## Summary|## Summary\n_Implements: #${ISSUE_NUMBER} — ${ISSUE_TITLE}_|" \
  | sed "s|## Linked Items|## Linked Items\n- #${ISSUE_NUMBER}|")

if [ -n "$ISSUE_BODY" ]; then
  PR_BODY="${PR_BODY}\n\n## Acceptance Criteria (from issue)\n${ISSUE_BODY}"
fi

# 6. Open PR via GitHub REST API
REVIEWER=$(jq -r '.defaultReviewers[0]' "$CONFIG")

curl -s -H "Authorization: token ${GH_PAT}" -X POST \
  "https://api.github.com/repos/${ORG}/${REPO}/pulls" \
  -H "Content-Type: application/json" \
  -d "$(jq -n \
    --arg title "[#${ISSUE_NUMBER}] ${ISSUE_TITLE}" \
    --arg desc  "$PR_BODY" \
    --arg src   "${BRANCH}" \
    --arg tgt   "${DEFAULT_BRANCH}" \
    '{title:$title, body:$desc, head:$src, base:$tgt}')" \
  | jq -r '"✓ PR created: " + .html_url'
```

### Step 6 — Pilot on `agency-gig-api` and `agency-gig-web` (Week 4)

Use 3 real GitHub issues per repo:
- Issue #1042: "As a band, I want to register a profile"
- Issue #1055: "As a venue manager, I want to post a gig slot"
- Issue #1089: "Bug: Booking conflict not detected for same-day requests"

Run `/branch-and-pr 1042` in `agency-gig-api`, verify:
- [ ] Branch name matches convention
- [ ] PR has all sections populated
- [ ] Issue #1042 is linked
- [ ] Reviewer is assigned

### Step 7 — Full rollout to `agency-gig-ui` (Week 6)

Add `.claude/branch-pr-config.json` to the component library repo.
Track: % of PRs requiring no manual edits (target ≥ 90%).

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Claude Code skill | Orchestrate branch + PR from a single command |
| GitHub REST API (`peter-at-integrant` org) | Fetch issues, create PRs |
| Bash + `jq` | Portable automation script |
| Git CLI | Branch creation and push |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- GitHub REST API: issue fetching, PR creation with linked issues
- Git branching: naming conventions at team scale
- Shell scripting for developer workflow automation
- AI skill design: orchestrating multiple API calls in sequence

---

## Demo Scenario

> Developer is assigned issue #1055 "As a venue manager, I want to post a gig slot".
> In `agency-gig-web`, runs: `/branch-and-pr 1055`
>
> Result in under 10 seconds:
> - Branch `feat/1055-venue-manager-post-gig-slot` created and pushed
> - PR opened: `[#1055] As a venue manager, I want to post a gig slot`
> - Description has all sections filled, acceptance criteria from issue appended
> - Linked to #1055, reviewer assigned

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Title with special chars breaks slug | `sed` pipeline sanitises; tested with edge case titles |
| `GH_PAT` in environment | Use GitHub Secrets; document PAT rotation schedule |
| Wrong default branch | Make configurable in `branch-pr-config.json` per repo |
| Reviewer email changes | Config is in each repo — update per sprint planning review |
