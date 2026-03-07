# Goal 7: Automated Testing with Playwright MCP

**Phase:** Level 0 — Maximizing AI Tool Usage
**Owner:** SDET
**Target:** Week 8

## Description

Integrate Playwright via an MCP server to auto-generate end-to-end test cases from feature specifications and execute them as part of the CI pipeline, making comprehensive test coverage an automatic output of every feature delivery.

## SMART Breakdown

- **Specific:** Configure a Playwright MCP server that reads feature specs or acceptance criteria and generates runnable Playwright test scripts, integrated into the GitHub Actions CI workflow with pass/fail gating.
- **Measurable:** Automated tests cover 80% of new UI feature acceptance criteria; test execution is fully integrated into CI with pass/fail blocking PRs.
- **Achievable:** Playwright is a mature framework already used in the industry; MCP SDK enables AI tool integration; CI workflow already exists in GitHub Actions.
- **Relevant:** Reduces SDET manual test authoring effort and catches UI regressions earlier in the development cycle.
- **Time-bound:** Pilot on `integrated-form/` by end of Week 5; 80% coverage target met by Week 8.

## Deliverables

- [ ] Playwright MCP server created and configured
- [ ] Test generation prompt/skill defined using feature spec format
- [ ] Pilot: 3+ test cases auto-generated and validated for `integrated-form/`
- [ ] GitHub Actions step added to run Playwright tests on PR
- [ ] GitHub Actions gate: PRs blocked when Playwright tests fail
- [ ] Coverage report showing 80% of acceptance criteria covered
- [ ] SDET team walkthrough completed

## Acceptance Criteria

- Given a feature's acceptance criteria, the MCP generates a runnable Playwright test file with no manual coding required.
- Generated tests execute successfully in the CI pipeline on every PR.
- 80% of new UI feature acceptance criteria are covered by auto-generated tests by Week 8.
- Failing tests block PR merges.
