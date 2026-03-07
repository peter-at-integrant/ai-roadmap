# Goal 11: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What is an "agent" in the LangChain/AutoGen sense? How does it differ from a single LLM call?**

> In LangChain/AutoGen, an agent is a loop: the LLM receives a goal, decides which tool to call, gets the result, decides what to do next, and repeats until the goal is achieved or it determines it can't proceed. A single LLM call is stateless — input in, output out, done. The agent adds a reasoning loop, tool use, and the ability to handle multi-step tasks where the next action depends on the result of the previous one.

---

**Q2. What does "multi-step" mean in an agent context? Give an example from the SDLC.**

> Multi-step means the agent takes a sequence of actions where each step may depend on the result of the previous one, rather than producing a single response. SDLC example: "Create a GitHub Issue from this requirement, create a branch for it, implement the feature, and open a PR." The agent would call the GitHub API to create the issue, extract the issue number, create the branch with the correct naming convention, write the code, commit it, and call `gh pr create` — each step informed by the output of the last.

---

**Q3. How do you prevent an agent from making destructive changes (e.g., deleting branches, force-pushing)?**

> Several layers: (1) **Tool scope** — only expose the tools the agent needs; don't give it a `delete_branch` tool if it doesn't need one. (2) **Permission boundaries** — use a GitHub token with the minimum required scopes; a token with only `repo:read` and `pull_requests:write` can't delete branches even if asked. (3) **Human-in-the-loop gates** — require confirmation before irreversible actions (merging, deploying). (4) **Dry-run mode** — have the agent describe what it would do and wait for approval before executing. Defence in depth: no single layer is enough on its own.

---

**Q4. What frameworks did you evaluate, and why did you choose the one you did?**

> The primary options evaluated were LangChain, AutoGen, and Claude Code's native agent loop. LangChain offers broad ecosystem and flexibility but adds significant abstraction complexity. AutoGen is strong for multi-agent conversation patterns. Claude Code's native tool-use loop was chosen for this project because it integrates directly with the existing workspace setup, requires no additional infrastructure, and the team already has Claude Code in place — minimising the learning curve and ops overhead for a first autonomous agent implementation.
