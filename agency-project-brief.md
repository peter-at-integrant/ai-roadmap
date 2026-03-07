# Agency — Band & Venue Booking Platform

**Type:** Demo project for AI Adoption Roadmap (Phase 0 — Level 0)
**Agreed by:** Hussein (Engineering Manager) & Peter (Senior Tech Lead)
**Date:** 2026-03-05

---

## What It Is

**Agency** is a two-sided marketplace where bands register their profiles and availability, and venue managers post open gig slots. Both sides can browse, request, confirm, and manage bookings through a clean web interface.

The platform handles the full lifecycle of a gig booking:

```
Band registers → Venue posts slot → Band requests gig →
Venue confirms/declines → Gig confirmed → Post-gig review
```

---

## Why This Project

Agency was chosen as the demo project for the AI adoption roadmap because:

- **3 distinct repos** with real cross-repo dependencies — exercises Goal 1's multi-repo workspace setup genuinely
- **Two tech stacks** (.NET 9 backend + Next.js frontend) — produces meaningful coding standards for Goals 2, 4, and 8
- **Rich booking domain** — generates real requirements, user stories, and GitHub Issues for Goals 3 and 6
- **Real UI with multi-step flows** — gives Playwright (Goal 7) genuine acceptance criteria to automate
- **Separate release cadences** across 3 repos — makes Goal 5's doc automation meaningful
- **Fun enough** that the team actually enjoys building it

---

## Repositories

| Repo | Description | Primary Owner |
|---|---|---|
| `agency-gig-api` | Backend REST API — all business logic, data access, auth | Peter (TL) |
| `agency-gig-web` | Next.js web application — band and venue interfaces | Dev team |
| `agency-gig-ui` | Shared React component library — design system used by `agency-gig-web` | Dev team |

---

## Tech Stack

### `agency-gig-api` — Backend API
| Technology | Purpose |
|---|---|
| .NET 9 Web API | HTTP endpoints, routing, middleware |
| Entity Framework Core 9 | ORM, migrations, data access |
| PostgreSQL 16 | Primary relational database |
| ASP.NET Core Identity | Authentication, JWT tokens |
| MediatR | CQRS — commands and queries |
| FluentValidation | Request validation |
| xUnit + FluentAssertions + NSubstitute | Unit and integration testing |
| Testcontainers | PostgreSQL container for integration tests |

### `agency-gig-web` — Frontend Application
| Technology | Purpose |
|---|---|
| Next.js 15 (App Router) | React framework, SSR/SSG |
| TypeScript 5 | Type safety |
| Tailwind CSS 4 | Styling |
| React Hook Form | Form management |
| TanStack Query | Server state, caching |
| Playwright | End-to-end testing |
| Vitest | Unit testing |

### `agency-gig-ui` — Component Library
| Technology | Purpose |
|---|---|
| TypeScript 5 | Type safety |
| React 19 | Component framework |
| Tailwind CSS 4 | Styling tokens and utilities |
| Storybook 8 | Component documentation and development |
| Vitest + Testing Library | Component unit tests |
| tsup | Library build tooling |

---

## Domain Model

### Core Entities

```
Band
  - BandId, Name, Genre[], Bio, Location
  - ContactEmail, ProfileImageUrl
  - Availability[] (dates available to play)

Venue
  - VenueId, Name, Location, Capacity
  - Description, ContactEmail
  - GigSlots[] (open dates being offered)

GigSlot
  - GigSlotId, VenueId, Date, StartTime, EndTime
  - PayRate, Description, Status (Open | Booked | Cancelled)

BookingRequest
  - BookingRequestId, BandId, GigSlotId
  - Status (Pending | Confirmed | Declined | Cancelled)
  - Message, CreatedAt, UpdatedAt

Review
  - ReviewId, BookingRequestId
  - ReviewerType (Band | Venue)
  - Rating (1–5), Comment, CreatedAt
```

### Booking Status Flow

```
[Open Slot]
    ↓  Band sends request
[Pending]
    ↓  Venue responds
[Confirmed] ──── or ──── [Declined]
    ↓  Either party cancels
[Cancelled]
    ↓  After gig date
[Completed] → Review unlocked
```

---

## Key Features

### Band-side
1. **Band Registration & Profile** — name, genres, location, bio, profile image, social links
2. **Availability Calendar** — bands mark which dates they're free to play
3. **Browse Venues & Slots** — filter by location, date, capacity, pay rate
4. **Send Booking Requests** — request a gig slot with a message
5. **Manage Bookings** — view pending/confirmed/declined requests, cancel if needed
6. **Leave Reviews** — rate venues after a completed gig

### Venue-side
7. **Venue Registration & Profile** — name, location, capacity, description
8. **Post Gig Slots** — create open slots with date, time, pay rate, description
9. **Review Booking Requests** — see incoming requests, confirm or decline
10. **Manage Calendar** — view all booked and available slots
11. **Leave Reviews** — rate bands after a completed gig

### Platform
12. **Conflict detection** — API prevents double-booking a band or slot
13. **Notifications** — email on booking status changes (Phase 2 scope)
14. **Search & filtering** — by location, genre, date, capacity

---

## Architecture Overview

```
agency-gig-web (Next.js)
    |── uses ──> agency-gig-ui (component library)
    |── calls ──> agency-gig-api (REST API)
                    |── reads/writes ──> PostgreSQL
                    |── (future) publishes ──> message bus
```

### API Layer Structure (`agency-gig-api`)
```
src/
  API/                    # Controllers, middleware, DI config
  Application/
    Features/
      Bands/             # Commands and queries for bands
      Venues/            # Commands and queries for venues
      Bookings/          # Commands and queries for bookings
      Reviews/           # Commands and queries for reviews
    Interfaces/          # Repository and service contracts
  Domain/                # Entities, value objects, domain events
  Infrastructure/        # EF Core, repositories, external services
```

---

## Coding Standards Summary

### .NET (`agency-gig-api`)
- CQRS via MediatR — commands mutate, queries read
- `Result<T>` (FluentResults) for error handling — no exceptions for flow control
- FluentValidation co-located with each command/query
- Repositories in Infrastructure, interfaces in Application
- No raw SQL — EF Core with migrations only
- Async throughout — no `.Result` or `.Wait()`

### TypeScript / Next.js (`agency-gig-web`)
- Functional components only, no class components
- Custom hooks for stateful logic: `use<Feature>`
- TanStack Query for all server state — no manual fetch in components
- No `any` type — use `unknown` and narrow
- React Hook Form for all forms

### Component Library (`agency-gig-ui`)
- Every component exported with TypeScript props interface `<ComponentName>Props`
- Storybook story required for every component
- No business logic — pure UI only

---

## GitHub Project Reference

- **Organisation:** `peter-at-integrant`
- **Repos:** `agency-workspace`, `agency-gig-api`, `agency-gig-web`, `agency-gig-ui`
- **Branch convention:** `feat/<id>-<slug>`, `fix/<id>-<slug>`, `chore/<id>-<slug>`
- **PR template:** Summary / Changes / Test Plan / Linked Items

---

## Acceptance Test Scenarios (for Goal 7 — Playwright)

| Flow | Key steps | Expected outcome |
|---|---|---|
| Band registration | Fill registration form → submit | Band profile created, redirect to dashboard |
| Post a gig slot | Venue fills slot form → submit | Slot appears in venue calendar as Open |
| Send booking request | Band clicks slot → sends request | Request appears as Pending for venue |
| Confirm booking | Venue confirms request | Both band and venue see Confirmed status |
| Conflict detection | Band requests two overlapping slots | Second request blocked with error message |
| Post-gig review | Band submits review after gig | Review visible on venue profile |
