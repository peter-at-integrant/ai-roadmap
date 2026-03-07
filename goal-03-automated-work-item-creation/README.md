# Goal 3: Automated Work Item Creation

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** PO / TPL
**Target:** Week 5

## Description

Build a workflow where requirement documents are fed to an AI tool and output structured work items (user stories, tasks, acceptance criteria) following team templates, then saved to the knowledge base for traceability.

## SMART Breakdown

- **Specific:** Define a standardized work item template and configure an AI-assisted workflow that reads a requirements document and produces ready-to-import work items in GitHub Issues format.
- **Measurable:** 80% of AI-generated work items require no manual restructuring before being moved to the backlog (tracked over first 3 sprint cycles).
- **Achievable:** Leverages existing GitHub integration and AI tool capabilities; template is defined once and reused.
- **Relevant:** Reduces PO and TPL time on backlog grooming and improves requirements traceability from source document to task.
- **Time-bound:** Pilot with one team by end of Week 3; full rollout by end of Week 5.

## Deliverables

- [ ] Work item template defined (user story, task, bug formats)
- [ ] AI prompt / skill created for converting requirements to work items
- [ ] Pilot run completed with one team using a real requirements document
- [ ] Work items exported to GitHub Issues and reviewed by PO
- [ ] Knowledge base entry created linking source requirement to generated items
- [ ] Rollout to all teams with a short walkthrough session

## Acceptance Criteria

- Given a requirements document, an AI tool produces a structured list of work items matching the team template within one session.
- 80% of generated items are accepted by the PO without restructuring.
- All generated work items are traceable back to the source requirement in the KB.

---

## Learning Objectives

By the end of this goal, you should be able to:

- Explain what a Claude Code skill is and how it differs from a plain prompt
- Trace the full flow: requirements doc → JSON work items → GitHub Issues
- Identify what the automation handles and what still requires human judgment

---

## Self-Assessment

Answer the following questions **in your own words** without AI assistance. Reviewed by EM/TL.

1. What is a skill in Claude Code? How is it different from a prompt you type directly?
2. Walk through how a requirements document gets converted to GitHub Issues — what are the steps and tools involved?
3. What information must each work item contain to be actionable for a developer?
4. What does the automation get wrong or miss? What would you check manually before importing?
