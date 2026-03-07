# Discussion: Demo Project Selection

**Date:** 2026-03-04
**Participants:** Hussein (Engineering Manager), Peter (Tech Lead)
**Subject:** Agree on a single demo project to ground all Phase 0 SMART goals and implementation plans
**Status:** Resolved — Agency (Band & Venue Booking Platform) selected

---

## Context

Every Phase 0 goal needs a real project to demonstrate against. Without one, implementation plans are generic and acceptance criteria are unmeasurable. The EM and TL need to agree on a purpose-built demo project before goal definition can be finalised.

Why this came up: the workspace already contained two existing projects (Profile Matching Tool and a Database Performance POC), but neither was suitable — they were too complex, pre-existing, or tied to a different domain. A clean project built specifically for this roadmap would make every demo scenario concrete and reproducible.

---

## Discussion

**Hussein:** Alright, we need a demo project. Something fun, three repos, realistic enough to actually exercise everything. What have you got?

**Peter:** Okay, first thought — fantasy football manager. Pick players, track scores, leagues—

**Hussein:** Everyone does fantasy football. Also I don't care about football.

**Peter:** Fair. What about a cocktail recipe app? You search ingredients you have, it tells you what you can make—

**Hussein:** That's like forty endpoints. Also it's basically a search box and a list. Where's the Playwright story? "User clicks search, list appears." Done. No drama.

**Peter:** Right, needs more user journey. What about... a **band gig booking platform**? You're a band, you list yourself, venues post slots, they match up—

**Hussein:** Oh that's actually interesting. There's users on both sides, there's a booking flow, there's statuses changing — *pending, confirmed, cancelled* — that's a proper state machine. Will it teach us architecture documentation?

**Peter:** Absolutely — auth service, booking engine, notification layer. Three clear domains. And the frontend has real forms: band registration, venue listings, gig requests. Playwright will have a field day.

**Hussein:** What's the third repo? Backend and frontend are obvious.

**Peter:** A shared component library. `agency-gig-ui`. Bands and venues both use the same design system. Its own release cycle, its own AGENTS.md, its own versioning. That's actually how you'd build it.

**Hussein:** So the frontend depends on the component library — release notes automation on *two* repos that feed into each other. I like that. And the database?

**Peter:** Bands, venues, gigs, bookings, availability slots. Postgres, proper relational schema, real migrations. The AI will have something to reason about.

**Hussein:** I love that it's called Agency because it sounds important but it's just helping a mediocre band called The Null Pointer find someone to play at a pub.

**Peter:** Exactly the kind of domain complexity we need — modest enough to build, rich enough to demonstrate everything.

**Hussein:** What specifically does each goal get to demo?

**Peter:** Goal 1 — submodules for all three repos. Goal 2 — AGENTS.md with real stack (`.NET 9`, `Next.js 15`, `TypeScript`). Goal 3 — work items from a real booking-flow requirements doc. Goal 4 — architecture docs with a real booking state machine. Goal 5 — release notes from an actual PR adding the post-gig review endpoint. Goal 6 — branch names like `feature/band-registration-endpoint`. Goal 7 — Playwright test for the venue creating a gig slot. Goal 8 — skills encoding `.NET` and `Next.js 15` conventions.

**Hussein:** Every goal has a concrete story. That's what I wanted. Let's go.

---

## Decisions Made

| Decision | Detail |
|---|---|
| Project name | Agency — Band & Venue Booking Platform |
| Repos | `agency-gig-api` (.NET 9, EF Core, PostgreSQL), `agency-gig-web` (Next.js 15, TypeScript, Tailwind), `agency-gig-ui` (React, Storybook, Vitest) |
| Core domain | Bands list themselves; venues post gig slots; bookings go through a state machine: Pending → Confirmed / Declined → Completed / Cancelled |
| Key complexity | Two user roles (Band, Venue), booking status transitions, component library as a dependency with its own release cycle |
| Why not existing projects | Too complex, pre-existing context, or domain mismatch with the SDLC goals |

---

## Outcome

Agency selected and agreed. `agency-project-brief.md` created in `ai-roadmap/`. All 8 Phase 0 implementation plans updated to reference Agency-specific examples, repos, and demo scenarios.
