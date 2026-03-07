# Goal 5: Business Documentation Automation via MCPs

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** DevOps / Dev
**Target:** Week 6

## Description

Configure MCP (Model Context Protocol) servers that automatically generate and update business documentation — such as release notes, API docs, and feature summaries — as part of each PR or deployment, making documentation a zero-friction part of the Definition of Done.

## SMART Breakdown

- **Specific:** Build and deploy at least one MCP server that hooks into the PR/merge workflow and auto-generates the required documentation artifact (e.g., release notes, changelog entry, feature summary).
- **Measurable:** Documentation is auto-generated for 100% of merged PRs; zero PRs closed without an accompanying doc update (enforced via pipeline gate by Week 6).
- **Achievable:** MCP SDK is available and documented; CI/CD pipelines already exist in GitHub Actions and can be extended with a gate step.
- **Relevant:** Eliminates manual documentation effort and ensures the Definition of Done includes up-to-date business docs without slowing down delivery.
- **Time-bound:** First MCP server operational by end of Week 4; pipeline gate enforcing doc completion by Week 6.

## Deliverables

- [ ] MCP server created for at least one documentation type (e.g., release notes)
- [ ] MCP server integrated with the PR/merge workflow in GitHub Actions
- [ ] Documentation template defined and embedded in the MCP
- [ ] Pipeline gate added: PR cannot be merged without doc artifact present
- [ ] Tested end-to-end on at least one real PR
- [ ] Team walkthrough completed so all developers understand the workflow

## Acceptance Criteria

- A developer merges a PR and a documentation artifact is auto-generated and attached without any manual steps.
- The pipeline blocks merges where the documentation gate fails.
- Generated documentation meets the quality bar defined by the team (reviewed by TPL).

---

## Learning Objectives

By the end of this goal, you should be able to:

- Explain what an MCP server is and how it exposes tools to AI tools
- Understand the structure of a tool definition in the MCP SDK (name, description, input schema, handler)
- Be able to add a new tool to the doc server

---

## Self-Assessment

Answer the following questions **in your own words** without AI assistance. Reviewed by EM/TL.

1. What is an MCP server? How does it differ from writing a skill file?
2. Walk through how `generate_release_notes` works — what inputs, what logic, what output?
3. Why is the doc automation in CI (GitHub Actions artifact gate) rather than run manually?
4. If you wanted to add a `generate_api_changelog` tool, what would you need to write?
