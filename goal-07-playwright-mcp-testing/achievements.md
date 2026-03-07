# Achievements — Goal 7: Automated Testing with Playwright MCP

**Status:** ✅ Done
**Goal file:** [goal-07-playwright-mcp-testing.md](./README.md)
**Implementation plan:** [implementation-plan.md](./implementation-plan.md)

---

## What was built

| File | Repo | Purpose |
|---|---|---|
| [`mcp-playwright-server/`](../../mcp-playwright-server/) | `agency-workspace` | MCP server with `generate_e2e_tests` and `run_tests` tools |
| `playwright.config.ts` | [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web/blob/main/playwright.config.ts) | Playwright config — base URL, browsers, test directory |
| `e2e/` | [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web/tree/main/e2e) | Sample E2E tests for Agency booking flows |
| `.github/workflows/ci.yml` | [agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web/blob/main/.github/workflows/ci.yml) | GitHub Actions CI — runs Playwright tests on every PR |
| [`.claude/skills/generate-tests.md`](../../.claude/skills/generate-tests.md) | `agency-workspace` | `/generate-tests` skill — generates E2E tests from acceptance criteria |

## How to use

**Generate E2E tests from an acceptance criterion:**
```
/generate-tests "Venue can create a gig slot with date, capacity, and genre preference"
```

**Run tests locally:**
```bash
cd services/agency-gig-web
npx playwright test
npx playwright test --ui   # interactive mode
```

**Run against staging:**
```bash
BASE_URL=https://staging.agency.example.com npx playwright test
```

## How to verify

Open a PR in `agency-gig-web`. The `ci` GitHub Actions workflow should trigger automatically and report Playwright test results. Check the [Actions tab in agency-gig-web](https://github.com/peter-at-integrant/agency-gig-web/actions).
