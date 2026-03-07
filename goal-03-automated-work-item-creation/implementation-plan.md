# Implementation Plan — Goal 3: Automated Work Item Creation

**Goal:** Feed an Agency requirements document to an AI tool and receive structured, import-ready GitHub Issues.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** PO / TPL
**Target:** Week 5

---

## Approach

Create a **Claude Code skill** that takes a requirements document as input, applies a structured prompt with the team's work item template, and outputs work items in GitHub Issues-compatible JSON. A Python script posts them to the `peter-at-integrant` GitHub org via REST API. The workflow: `requirements.md` → AI skill → `work-items.json` → GitHub Issues import.

---

## Step-by-Step Implementation

### Step 1 — Define the work item template

Create `tools/templates/work-item-template.md` in `agency-workspace`:

```markdown
## User Story
**Title:** As a [band/venue manager/admin], I want [capability] so that [benefit]
**Description:** [Business context and detail]
**Acceptance Criteria:**
- Given [context], When [action], Then [outcome]
**Story Points:** [1, 2, 3, 5, 8, 13]
**Tags:** [feature-area] [repo: agency-gig-api | agency-gig-web | agency-gig-ui]

## Task (child of story)
**Title:** [Verb + object: e.g. "Implement BookingRequest confirmation endpoint"]
**Description:** [Technical detail]
**Acceptance Criteria:** [Technical DoD]
**Repo:** [agency-gig-api | agency-gig-web | agency-gig-ui]
**Estimated Hours:** [hours]

## Bug
**Title:** Bug: [short description]
**Steps to Reproduce:** [numbered steps]
**Expected / Actual:** [what should happen vs what happens]
**Severity:** Critical | High | Medium | Low
**Repo:** [affected repo]
```

### Step 2 — Create the Claude Code skill

Create `.claude/skills/create-work-items.md` in `agency-workspace`:

```markdown
# Skill: Create Work Items from Requirements

Given a requirements document for the Agency platform, produce structured
GitHub Issues following the team template.

## Instructions
1. Read the requirements document carefully
2. Identify distinct features, user-facing capabilities, and constraints
3. For each feature, create one User Story (As a band / venue manager / admin...)
4. Break each story into 2–5 child Tasks tagged with the relevant repo (agency-gig-api, agency-gig-web, agency-gig-ui)
5. Flag ambiguous requirements as open questions
6. Output JSON matching the GitHub Issues import format

## Domain context
- Users are either Bands or Venue Managers
- The booking lifecycle is: Pending → Confirmed | Declined → Completed | Cancelled
- Three repos: agency-gig-api (.NET), agency-gig-web (Next.js), agency-gig-ui (components)
- Tag each Task with the repo it belongs to

## Output format
{
  "workItems": [
    {
      "type": "User Story",
      "title": "As a band, I want to...",
      "description": "...",
      "acceptanceCriteria": "Given... When... Then...",
      "storyPoints": 0,
      "tags": "bookings agency-gig-api",
      "children": [
        {
          "type": "Task",
          "title": "Implement BookingRequest entity and migration",
          "description": "...",
          "repo": "agency-gig-api",
          "estimatedHours": 0
        }
      ]
    }
  ],
  "openQuestions": ["..."]
}
```

### Step 3 — Build the GitHub Issues import script

Create `tools/scripts/import-work-items.py`:

```python
import json, requests, sys, os

GH_ORG = "peter-at-integrant"
GH_REPO = "agency-workspace"
GH_PAT = os.environ["GH_PAT"]

def create_issue(fields, labels=None):
    url = f"https://api.github.com/repos/{GH_ORG}/{GH_REPO}/issues"
    body = fields.get("description", "")
    if fields.get("acceptanceCriteria"):
        body += f"\n\n## Acceptance Criteria\n{fields['acceptanceCriteria']}"
    payload = {
        "title": fields["title"],
        "body": body,
        "labels": labels or [],
    }
    resp = requests.post(url, json=payload, headers={
        "Authorization": f"token {GH_PAT}",
        "Accept": "application/vnd.github+json"
    })
    resp.raise_for_status()
    return resp.json()["number"]

def import_all(path):
    data = json.load(open(path))
    for story in data["workItems"]:
        labels = ["user-story"] + story.get("tags", "").split()
        story_number = create_issue(story, labels=labels)
        print(f"✓ Issue #{story_number}: {story['title']}")
        for task in story.get("children", []):
            task_labels = ["task", task.get("repo", "")]
            task_number = create_issue(task, labels=task_labels)
            print(f"  ✓ Issue #{task_number} [{task.get('repo','')}]: {task['title']}")
    if data.get("openQuestions"):
        print("\nOpen questions to resolve:")
        for q in data["openQuestions"]:
            print(f"  ? {q}")

if __name__ == "__main__":
    import_all(sys.argv[1])
```

### Step 4 — Pilot with an Agency requirements doc

Create `docs/requirements/booking-flow-v1.md` — a real 1–2 page spec for the Agency booking request feature. Example outline:

```markdown
# Requirements: Booking Request Flow

## Overview
Bands browsing available gig slots should be able to send a booking request
to a venue. Venues review incoming requests and confirm or decline.

## Functional Requirements
1. A band can send a booking request for any Open gig slot
2. A band cannot request a slot if they already have a confirmed booking on the same date
3. A venue receives a notification when a new request arrives
4. A venue can confirm or decline a request
5. When confirmed, the slot status changes to Booked and other pending requests for the same slot are automatically declined
6. Either party can cancel a confirmed booking up to 48 hours before the gig date

## Non-Functional Requirements
- Conflict check must complete within 200ms
- Status transitions must be atomic (no partial updates)
```

Run the skill, review the JSON, import to GitHub Issues, get PO sign-off.

### Step 5 — Track acceptance rate

PO records per sprint:
- Total AI-generated items
- Items accepted without restructuring
- Items requiring edits (log the reason)

Target: ≥ 80% acceptance by sprint 3.

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Claude Code skill | Convert `booking-flow-v1.md` → structured JSON |
| GitHub REST API | Create Issues in `peter-at-integrant` org |
| Python + `requests` | Import script |
| GitHub PAT (env var) | Auth — store in GitHub Secrets / `.env` (never commit) |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- Prompt design for structured JSON output with domain-specific schema
- GitHub REST API: issue creation, label-based hierarchy linking
- Agency domain knowledge: booking lifecycle, multi-repo task tagging
- Process design: AI-assisted workflow with human review gate

---

## Demo Scenario

> Take `docs/requirements/booking-flow-v1.md`.
> Run: `/create-work-items @docs/requirements/booking-flow-v1.md`
>
> Expected output: 4–6 User Stories with child Tasks correctly tagged to `agency-gig-api` (entity, migration, endpoint) and `agency-gig-web` (form, query hook, state management). PO reviews and accepts 80%+ without edits.

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| AI misses multi-repo task split | Skill prompt explicitly instructs to tag each task with its repo |
| Ambiguous requirements | AI outputs `openQuestions`; PO resolves before import |
| GitHub PAT expiry | Document expiry date; set calendar reminder 1 week before |
| JSON schema drift | Add JSON schema validation before import script runs |
