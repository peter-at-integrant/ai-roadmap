# Goal 6: Automated Branching and PR Creation

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** Dev
**Target:** Week 6

## Description

Implement an AI-assisted workflow that automatically creates branches following team naming conventions and opens pull requests with standardized descriptions, linked work items, and reviewer assignments — consistently across all repositories.

## SMART Breakdown

- **Specific:** Create an AI-driven workflow (skill or script) that, given a work item, generates the correct branch name, creates the branch in the right repo, and opens a PR with the required title format, description template, linked items, and assigned reviewers.
- **Measurable:** 90% of auto-generated PRs require no manual edits to title, description, or linked items (tracked from first rollout sprint).
- **Achievable:** GitHub REST API supports branch and PR creation; AI tools can be scripted to call it via MCP or a helper skill.
- **Relevant:** Removes repetitive PR setup overhead for developers and enforces consistency across all repositories and teams.
- **Time-bound:** Workflow live for at least 2 repos by end of Week 4; all repos covered by Week 6.

## Deliverables

- [ ] Branch naming convention documented and agreed upon by all teams
- [ ] PR description template finalized (sections: summary, changes, test plan, linked items)
- [ ] Automated branching skill/script created and tested
- [ ] Automated PR creation skill/script created and tested
- [ ] Rolled out to 2 pilot repos by Week 4
- [ ] Rolled out to all repos by Week 6
- [ ] Metric tracked: % of PRs requiring no manual edits

## Acceptance Criteria

- Developer triggers the workflow with a work item reference; a correctly named branch is created and a fully populated PR is opened automatically.
- 90% of generated PRs pass the team's PR checklist without manual edits.
- Workflow works across all team repositories.
