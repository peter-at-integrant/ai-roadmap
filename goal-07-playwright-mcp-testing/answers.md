# Goal 7: Self-Assessment Answers

**Reviewer:** EM/TL
**Completed by:**
**Date:**

---

**Q1. What's the difference between unit tests, integration tests, and E2E tests? Which does Playwright cover?**

> Unit tests verify a single function or class in isolation. Integration tests verify that multiple units work together correctly (e.g. a service + repository + database). E2E (end-to-end) tests verify a complete user journey through the running application, from UI to backend and back. Playwright covers E2E — it drives a real browser, clicks buttons, fills forms, and asserts on what the user actually sees.

---

**Q2. How does the Playwright MCP server generate tests? What does it take as input?**

> The Playwright MCP server takes a description of a user journey (or a page URL and an action to perform) as input. It can observe the live application, record interactions, and generate Playwright test code that reproduces those interactions. The AI uses the MCP server's tools to navigate pages and introspect the DOM, then writes test assertions based on what it observes. The output is a `.spec.ts` file ready to run in the test suite.

---

**Q3. Why does a failing Playwright test block a PR merge? What's the risk of not blocking?**

> Blocking ensures that a regression in a critical user journey is caught before it reaches main. The risk of not blocking is that broken flows (login, booking creation, payment) can be merged and reach production or staging undetected. E2E failures often indicate something that unit and integration tests missed — a wiring issue, a UI state that only appears in the browser, or a cross-service interaction problem.

---

**Q4. You add a new booking confirmation flow. What do you do to make sure it has test coverage?**

> 1. Run the Playwright MCP tool against the new flow in the development environment to generate a base test.
> 2. Review and refine the generated test — add edge case assertions, ensure selectors are stable (prefer `data-testid` over CSS classes).
> 3. Add the test file to the repo and confirm it passes locally.
> 4. Verify the CI workflow picks it up and runs it on PR. If the flow spans multiple services, ensure the test environment has all services running.
