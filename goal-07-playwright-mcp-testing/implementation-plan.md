# Implementation Plan — Goal 7: Automated Testing with Playwright MCP

**Goal:** Auto-generate Playwright E2E tests for Agency UI flows from acceptance criteria; tests run in CI and block failing PRs.
**Demo Project:** [Agency — Band & Venue Booking Platform](../agency-project-brief.md)
**Owner:** SDET
**Target:** Week 8

---

## Approach

Build a **Playwright MCP server** that exposes `generate_e2e_tests` and `run_tests` tools. Given acceptance criteria for an Agency feature (band registration, booking flow, etc.), it generates a runnable Playwright test file in `agency-gig-web`. A second step integrates the tests into the GitHub Actions workflow for `agency-gig-web`, blocking PRs when tests fail.

---

## Step-by-Step Implementation

### Step 1 — Initialise Playwright in `agency-gig-web`

```bash
cd services/agency-gig-web
npm install -D @playwright/test
npx playwright install --with-deps chromium
```

Create `playwright.config.ts`:

```typescript
import { defineConfig, devices } from "@playwright/test";

export default defineConfig({
  testDir: "./tests/e2e",
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  reporter: process.env.CI ? [["junit", { outputFile: "playwright-results.xml" }], ["html"]] : "list",
  use: {
    baseURL: process.env.BASE_URL ?? "http://localhost:3000",
    trace: "on-first-retry",
  },
  projects: [
    { name: "chromium", use: { ...devices["Desktop Chrome"] } },
  ],
});
```

Test structure:
```
agency-gig-web/
  tests/
    e2e/
      generated/    ← AI-generated test files
      manual/       ← Hand-authored tests
```

### Step 2 — Scaffold the Playwright MCP server

In `agency-workspace`:

```bash
mkdir mcp-playwright-server && cd mcp-playwright-server
npm install @modelcontextprotocol/sdk @anthropic-ai/sdk zod
```

### Step 3 — Implement the MCP server

Create `mcp-playwright-server/src/index.ts`:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import Anthropic from "@anthropic-ai/sdk";
import { z } from "zod";
import { execSync } from "child_process";
import { writeFileSync, mkdirSync } from "fs";
import path from "path";

const server = new McpServer({ name: "agency-playwright", version: "1.0.0" });
const client = new Anthropic();

const AGENCY_CONTEXT = `
The Agency app is a band/venue booking platform.
- Band Portal routes: /band/register, /band/dashboard, /band/browse, /band/bookings
- Venue Portal routes: /venue/register, /venue/dashboard, /venue/slots, /venue/requests
- Data-testid naming: kebab-case, e.g. data-testid="band-register-form", data-testid="submit-button"
- All forms use React Hook Form; validation errors appear in data-testid="<field>-error"
- Booking status badges: data-testid="booking-status-<id>"
`;

server.tool(
  "generate_e2e_tests",
  "Generate Playwright tests for an Agency feature from acceptance criteria",
  {
    featureName: z.string().describe("Feature name, used as test file name"),
    userType: z.enum(["band", "venue", "both"]).describe("Which user role(s) this feature serves"),
    acceptanceCriteria: z.array(z.string()).describe("Given/When/Then acceptance criteria"),
    existingTestPath: z.string().optional().describe("Path to existing test for style reference"),
  },
  async ({ featureName, userType, acceptanceCriteria, existingTestPath }) => {
    const acList = acceptanceCriteria.map((ac, i) => `${i + 1}. ${ac}`).join("\n");

    const message = await client.messages.create({
      model: "claude-sonnet-4-6",
      max_tokens: 4096,
      messages: [{
        role: "user",
        content: `Generate a complete Playwright test file in TypeScript for the Agency platform.

Feature: ${featureName}
User type: ${userType}

App context:
${AGENCY_CONTEXT}

Acceptance Criteria:
${acList}

Requirements:
- Use @playwright/test with TypeScript
- One describe block for the feature, one test per acceptance criterion
- Use data-testid selectors (not CSS classes or text content)
- Use page.waitForSelector or expect().toBeVisible() — no hard waits
- Include beforeEach to navigate to the correct starting route
- Add a comment above each test explaining which AC it covers
- Output ONLY the TypeScript code`,
      }],
    });

    const code = message.content[0].type === "text" ? message.content[0].text : "";
    const fileName = featureName.toLowerCase().replace(/\s+/g, "-") + ".spec.ts";
    const outDir = path.join(process.cwd(), "services/agency-gig-web/tests/e2e/generated");
    mkdirSync(outDir, { recursive: true });
    writeFileSync(path.join(outDir, fileName), code, "utf-8");

    return { content: [{ type: "text", text: `Generated: tests/e2e/generated/${fileName}\n\n${code}` }] };
  }
);

server.tool(
  "run_tests",
  "Run Playwright tests in agency-gig-web and return results",
  {
    pattern: z.string().optional().describe("Glob pattern, e.g. tests/e2e/generated/*.spec.ts"),
  },
  async ({ pattern }) => {
    const p = pattern ?? "tests/e2e/";
    try {
      const out = execSync(
        `cd services/agency-gig-web && npx playwright test ${p} --reporter=list`,
        { encoding: "utf-8" }
      );
      return { content: [{ type: "text", text: out }] };
    } catch (err: any) {
      return { content: [{ type: "text", text: err.stdout ?? err.message }] };
    }
  }
);

async function main() {
  await server.connect(new StdioServerTransport());
}
main();
```

### Step 4 — Claude Code skill

Create `.claude/skills/generate-tests/SKILL.md` in `agency-workspace`:

> **Structure note:** Skills must be a directory containing a `SKILL.md` file with YAML frontmatter (`name` + `description`). A flat `.md` file will not be registered as a slash command.

```markdown
---
name: generate-tests
description: This skill should be used when the user wants to generate Playwright E2E tests for Agency platform features.
---

# Skill: Generate Playwright Tests for Agency

## Usage
> /generate-tests <feature-name>

## What I will do
1. Read the acceptance criteria for the feature (from work item or spec file you provide)
2. Call the Playwright MCP `generate_e2e_tests` tool
3. Review the generated file — check selectors match the component library conventions
4. Run a quick smoke pass with `run_tests` on the generated file against localhost
5. Report: which ACs have coverage, which couldn't be automated (and why)

## data-testid conventions in Agency
- Forms: data-testid="<feature>-form"
- Submit buttons: data-testid="submit-button"
- Error messages: data-testid="<field>-error"
- Status indicators: data-testid="booking-status-<id>"
```

### Step 5 — Pilot: 3 test cases for Agency booking flow

From the project brief's acceptance test scenarios:

1. **Given** I fill all required fields and submit the band registration form,
   **When** the form is submitted,
   **Then** I am redirected to the band dashboard with a success message

2. **Given** a band has an open gig slot available,
   **When** the band submits a booking request,
   **Then** the request appears as Pending in the band's bookings list

3. **Given** a venue receives a Pending booking request,
   **When** the venue clicks Confirm,
   **Then** the booking status changes to Confirmed for both the band and venue

Run: `/generate-tests "band-booking-flow"`
Review `tests/e2e/generated/band-booking-flow.spec.ts`. Fix any selector issues. Commit.

### Step 6 — GitHub Actions integration in `agency-gig-web`

Add to `services/agency-gig-web/.github/workflows/ci.yml`:

```yaml
jobs:
  e2e-tests:
    name: Playwright E2E Tests
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci
        working-directory: services/agency-gig-web

      - name: Install Playwright browser
        run: npx playwright install --with-deps chromium
        working-directory: services/agency-gig-web

      - name: Run Playwright tests
        run: npx playwright test --reporter=junit,html
        working-directory: services/agency-gig-web
        env:
          BASE_URL: ${{ secrets.STAGING_URL }}

      - name: Upload test results
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-results
          path: services/agency-gig-web/playwright-results.xml

      - name: Upload Playwright report
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-report
          path: services/agency-gig-web/playwright-report
```

### Step 7 — Branch protection gate

In GitHub `agency-gig-web` repo settings:
- Branch protection rules → `main`
- Required: "Require status checks to pass before merging" → select `e2e-tests` job

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| `@playwright/test` | E2E test runner for `agency-gig-web` |
| MCP SDK | Expose test generation as a structured AI tool |
| Claude API | Generate TypeScript test code from ACs |
| GitHub Actions | Run tests on every PR, block on failure |
| JUnit reporter + `actions/upload-artifact` | Test results visible in GitHub Actions runs |

---

## Proof of Knowledge

By completing this goal, the implementor demonstrates:
- Playwright test authoring: `data-testid` selectors, `waitFor`, `expect`, page navigation
- MCP server construction: tool registration, file I/O, subprocess execution
- Agency UI knowledge: route structure, form conventions, component library selectors
- GitHub Actions workflow design with test gating

---

## Demo Scenario

> Feature: "Post-gig review submission" (5 acceptance criteria).
> Run: `/generate-tests "post-gig-review"`
>
> Review `tests/e2e/generated/post-gig-review.spec.ts` — 5 tests, each covering one AC.
> Run locally against dev server: all pass.
> Open PR in `agency-gig-web`. GitHub Actions runs `e2e-tests`. All 5 pass. PR unblocked.

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Generated selectors don't exist | Skill prompt references `data-testid` conventions; review generated file before committing |
| Tests flaky in CI | `retries: 2` in `playwright.config.ts`; `waitForSelector` not `waitForTimeout` |
| `agency-gig-ui` component changes break selectors | `data-testid` is a stability contract — document in `agency-gig-ui` AGENTS.md |
| Coverage hard to measure | Count ACs per sprint; compare to test count in `generated/` folder |
