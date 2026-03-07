# Goal 10 (MCP Creation): Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. When should you build an MCP server instead of writing a skill file?**

> Build an MCP server when the task requires accessing external data or systems at runtime — calling an API, querying a database, reading files programmatically, or executing code. A skill file is static text the AI reads and follows using its own capabilities. If the AI can accomplish the task with the tools it already has (file reading, bash commands, web fetch), a skill is sufficient. If you need custom logic, structured data from an external source, or operations the AI can't do natively, build an MCP server.

---

**Q2. What are the three components of an MCP tool definition?**

> 1. **Name** — the identifier the AI uses to invoke the tool (e.g. `get_open_issues`).
> 2. **Description** — a plain-English explanation of what the tool does, used by the AI to decide when to call it.
> 3. **Input schema** — a JSON Schema defining the parameters the tool accepts, including types, required fields, and descriptions. The AI uses this to know what arguments to pass when invoking the tool.

---

**Q3. Walk through how you'd build an MCP that pulls open GitHub Issues and formats them as a sprint board.**

> 1. Create a new Python MCP server using the `mcp` SDK.
> 2. Define a tool `get_sprint_board` with parameters for repo name and optional label filter.
> 3. In the tool handler, call the GitHub REST API (`GET /repos/{owner}/{repo}/issues?state=open`) using a personal access token stored as an environment variable.
> 4. Group the returned issues by label (e.g. `in-progress`, `todo`, `review`) and return a structured object.
> 5. Register the server in the MCP config so Claude Code can discover it.
> 6. The AI can now call `get_sprint_board` and render the result as a markdown board.

---

**Q4. What is n8n and how does it connect AI to business workflows?**

> n8n is a workflow automation platform (similar to Zapier or Make) that lets you build visual automation pipelines connecting different services. It has a built-in AI/LLM node that can call models like Claude or GPT as a step in a workflow. This means you can trigger an AI operation from a business event — e.g. when a new Jira ticket is created, n8n sends its description to Claude to generate acceptance criteria, then posts the result back to the ticket. It bridges AI capabilities into existing business tools without custom code for each integration.
