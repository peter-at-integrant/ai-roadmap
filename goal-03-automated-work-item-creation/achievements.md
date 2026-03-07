# Achievements — Goal 3: Automated Work Item Creation

**Status:** ✅ Done
**Goal file:** [goal-03-automated-work-item-creation.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

## What was built

| File | Repo | Purpose |
|---|---|---|
| [`tools/templates/work-item-template.md`](../../tools/templates/work-item-template.md) | `agency-workspace` | User Story / Task / Bug templates |
| [`.claude/skills/create-work-items.md`](../../.claude/skills/create-work-items.md) | `agency-workspace` | Claude Code skill — converts requirements into GitHub Issues JSON |
| [`tools/scripts/import-work-items.py`](../../tools/scripts/import-work-items.py) | `agency-workspace` | Python script — POSTs work items to GitHub Issues via REST API |
| [`docs/requirements/booking-flow-v1.md`](../../docs/requirements/booking-flow-v1.md) | `agency-workspace` | Pilot requirements doc — booking flow (6 FRs + 3 NFRs) |
| [`docs/requirements/booking-flow-v1-work-items.json`](../../docs/requirements/booking-flow-v1-work-items.json) | `agency-workspace` | Sample output — 4 stories, 8 tasks, 3 open questions |

## How to use

**Step 1 — Generate work items from a requirements doc:**

Open a Claude Code session and run the skill:
```
/create-work-items docs/requirements/booking-flow-v1.md
```
Claude reads the doc and outputs structured JSON work items.

**Step 2 — Import to GitHub Issues:**
```bash
export GH_PAT=<your-pat>
python tools/scripts/import-work-items.py \
  --json docs/requirements/booking-flow-v1-work-items.json \
  --repo peter-at-integrant/agency-workspace
```

## How to verify

Check [agency-workspace Issues](https://github.com/peter-at-integrant/agency-workspace/issues) — stories and tasks from the booking flow requirements should appear as open issues with correct labels and descriptions.
