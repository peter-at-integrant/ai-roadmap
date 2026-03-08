# Goal 10: MCP Creation and Workflow Automation

**Phase:** Level 1 — AI Integration Engineer
**Owner:** Dev / DevOps
**Target:** Month 3

## Description

Each team designs, builds, and deploys a custom MCP server that automates a real business workflow and connects AI tools to existing business operations via n8n or Airflow — turning AI capability into tangible process automation.

## SMART Breakdown

- **Specific:** Each team builds and deploys one custom MCP server that automates a specific business workflow (e.g., report generation, ticket routing, status summaries) and integrates with n8n or Airflow for orchestration.
- **Measurable:** MCP is live and invocable from AI tools; it replaces at least one previously manual workflow step, verified by the workflow owner.
- **Achievable:** MCP SDK is documented with examples; n8n and Airflow have standard connectors and APIs; teams already have context from Phase 1 MCP usage.
- **Relevant:** Moves beyond AI-assisted development into broader business process automation, delivering value outside the SDLC.
- **Time-bound:** MCP deployed and in active use by end of Month 3.

## Deliverables

- [ ] Workflow identified and documented per team (what is being automated, current manual steps)
- [ ] MCP server designed (inputs, outputs, tools exposed)
- [ ] MCP server implemented using the MCP SDK
- [ ] Integration with n8n or Airflow configured
- [ ] End-to-end test: AI tool invokes MCP, workflow executes, output verified
- [ ] Deployed to a shared environment accessible by the team
- [ ] Documentation: how to invoke the MCP, expected inputs/outputs, maintenance notes

## Acceptance Criteria

- Each team has a deployed, functioning MCP server that automates a real workflow.
- The MCP is invocable from at least one AI tool (Cursor, Claude Code, etc.) without manual steps.
- At least one previously manual workflow step is eliminated, confirmed by the workflow owner.

---

## Learning Objectives

By the end of this goal, you should be able to:

- Build an MCP server from scratch using the MCP SDK
- Know when to use MCP vs a skill file vs a standalone script
- Understand where n8n/Airflow fits in the SDLC automation picture

---

## Self-Assessment

Answer the following questions **in your own words** without AI assistance. Reviewed by EM/TL.

1. When should you build an MCP server instead of writing a skill file?
2. What are the three components of an MCP tool definition?
3. Walk through how you'd build an MCP that pulls open GitHub Issues and formats them as a sprint board.
4. What is n8n and how does it connect AI to business workflows?
