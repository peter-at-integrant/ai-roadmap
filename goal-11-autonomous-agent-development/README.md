# Goal 11: Autonomous Multi-Step Agent Development

**Phase:** Level 2 — AI Agent Engineer
**Owner:** Dev
**Target:** Month 5

## Description

Build at least one autonomous agent per team using an orchestration framework (LangChain, CrewAI, or AutoGen) that completes a multi-step SDLC task end-to-end without human intervention — such as analyzing a PR, running tests, summarizing results, and posting a review comment.

## SMART Breakdown

- **Specific:** Each team builds and deploys one autonomous agent using a chosen orchestration framework that executes at least 3 sequential, tool-using steps within an SDLC workflow.
- **Measurable:** Agent completes the end-to-end task with no human intervention in 85% of runs; cost per run is documented and within team-defined budget thresholds.
- **Achievable:** Python skills acquired during this phase; LangChain, CrewAI, and AutoGen are open-source with extensive documentation and community examples.
- **Relevant:** Directly reduces manual orchestration effort across the SDLC, enabling engineers to focus on higher-value work.
- **Time-bound:** Agent in production use and actively running by end of Month 5.

## Deliverables

- [ ] Use case selected and documented per team (multi-step SDLC workflow)
- [ ] Orchestration framework selected (LangChain, CrewAI, or AutoGen) with justification
- [ ] Agent designed: steps, tools used, inputs, outputs, failure handling
- [ ] Agent implemented in Python
- [ ] Local testing: agent completes task successfully in test environment
- [ ] Cost per run measured and documented
- [ ] Deployed to production (or staging) environment
- [ ] Metric tracked: success rate over first 2 weeks of production use
- [ ] Write-up submitted to team KB: design decisions, lessons learned

## Acceptance Criteria

- Agent executes a defined multi-step SDLC workflow with no human intervention in 85% of runs.
- Cost per run is documented and does not exceed the team-agreed budget threshold.
- Agent is in active production use, not just a demo.

---

## Learning Objectives

By the end of this goal, you should be able to:

- Understand how agent orchestration frameworks (LangChain, CrewAI, AutoGen) work
- Design a multi-step agent with defined tools and a clear stop condition
- Trace an agent's execution log and identify where it failed

---

## Self-Assessment

Answer the following questions **in your own words** without AI assistance. Reviewed by EM/TL.

1. What is an "agent" in the LangChain/AutoGen sense? How does it differ from a single LLM call?
2. What does "multi-step" mean in an agent context? Give an example from the SDLC.
3. How do you prevent an agent from making destructive changes (e.g., deleting branches, force-pushing)?
4. What frameworks did you evaluate, and why did you choose the one you did?
