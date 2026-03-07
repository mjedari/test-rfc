# Event Discovery Platform — Delivery Proposal

**Client/Stakeholder:** _[Name / Company]_  
**Vendor:** _[Your Company]_  
**Prepared by:** Mahdi Jedari  
**Date:** _[2026-03-05]_  
**Version:** 1.5

---

## 1) Executive Summary

This document proposes how we will deliver the **Event Discovery Platform** described in the RFC: a mobile-backed product that aggregates local events for a user’s city and presents them in a clean feed with search/filters and a map experience. The MVP launch regions are **Los Angeles** and **Montreal**, targeting students first.

We will deliver using **Scrum** in **2-week sprints**, with **continuous delivery** (Sprint Review demo + installable mobile build each sprint), providing predictable progress, early value, and transparent scope control.

### In Scope (MVP)
- Event feed (city + radius; default 14-day window), search & filters
- Event details (incl. ticket link + provider attribution)
- Favorites (save/unsave) and saved-events list
- Map view (events plotted; bbox/radius queries)
- Onboarding questionnaire + preferences management
- Authentication + profile + password reset/change
- Settings & privacy (notification preferences, data export/deletion)
- Event reporting (flag incorrect/outdated events)
- Push notifications (saved-event reminders + city-wide announcements)
- Admin portal for event review + manual event input

### Explicitly Out of Scope (MVP)
- AI/ML personalization or behavior-based recommendations beyond deterministic rules
- In-app ticket purchase (external ticket links only)
- Web user platform (user access to feed, search, map on web) — deferred to Phase 2

---

## 2) Team & Roles

| Role | Allocation | Primary Responsibilities |
| --- | --- | --- |
| GoLang Developer | Full-time | Backend APIs, data model, integrations, performance, security, CI/CD implementation, deployment support |
| React Native Developer | Full-time | Mobile app (iOS/Android), UI implementation, state management, build/release pipeline support, store readiness |
| Project Manager & Team Lead | Full-time | Scrum Master, delivery planning & tracking, RAID log, scope/change control, stakeholder comms, dependency management, release & deployment coordination, reporting, team unblocker |
| UI/UX Designer | Part-time | UX flows, wireframes, UI kit, prototypes, usability feedback, handoff specs |
| Admin Panel Developer (Nuxt/Next) | Part-time | Admin portal UI (review queue, CRUD, approvals), admin UX polish, stakeholder feedback loop |

**Note:** The MVP delivers a **mobile app** (React Native, iOS + Android) and an **admin web portal**. A user-facing web platform is planned for **Phase 2**.

---

## 3) Delivery Model (Scrum + Continuous Delivery)

### Scrum Roles
- **Product Owner (Client):** owns product decisions, prioritizes the Product Backlog, accepts/rejects work against acceptance criteria
- **Project Manager & Team Lead (Vendor):** ensures Scrum is followed, removes impediments, facilitates events, protects delivery flow; coordinates releases/deployments
- **Developers (Vendor):** GoLang Developer + React Native Developer + Admin Panel Developer (part-time) (accountable for delivering a “Done” Increment each Sprint)
- **Designer (Vendor, part-time):** supports Developers with UX flows, UI kit, prototypes, and handoff; not a Scrum role but part of the delivery team

### Timebox & Cadence
- **Sprint length:** 2 weeks (10 working days)
- **Each Sprint produces:** a “Done” Increment + release notes + installable build
  - iOS: TestFlight
  - Android: Internal testing track
- **Environments:** Dev → Staging → Production (backend); internal distribution for mobile builds

### Scrum Events
- **Sprint Planning** (1–2h): define Sprint Goal + select Product Backlog Items into Sprint Backlog
- **Daily Scrum** (15 min): inspect progress toward Sprint Goal and adapt the plan
- **Product Backlog Refinement** (60 min weekly): clarify and size upcoming work
- **Sprint Review** (45–60 min): demo the Increment, gather feedback, adapt Product Backlog
- **Sprint Retrospective** (30 min): improve process, quality, and delivery flow

### Scrum Artifacts (what stays visible)
- **Product Backlog:** ordered list of all desired work (MVP + later phases)
- **Sprint Backlog:** selected work for the Sprint + plan to deliver it
- **Increment:** integrated, tested output that meets Definition of Done

### Definition of Done (DoD)
- Acceptance criteria met
- Code reviewed against agreed quality checklist (correctness, security basics, performance, maintainability)
- Automated checks passing (lint/tests/build) and required tests updated for changed behavior
- Deployed to staging (backend) and distributed as a test build (mobile), with a rollback path identified
- Release notes updated

### Deployment & Release Responsibility
- **Vendor responsibility:** CI/CD setup, staging deployments each Sprint, and production release coordination.
- **Release gating:** production releases occur only after Sprint Review acceptance and stakeholder go/no-go.
- **Ownership:** Project Manager & Team Lead owns release calendar, stakeholder approvals, and deployment coordination; Developers execute and validate deployments/builds.

---

## 4) Scope Approach (MVP First)

We recommend agreeing on an **MVP release goal** that can ship to a limited audience, then iterating based on feedback. In Scrum, scope is managed by maintaining a single ordered **Product Backlog**; time and team capacity remain fixed per Sprint.

### Sprint 0 (Inception Sprint: Discovery & Setup) — Required
**Duration:** 2 weeks  
**Deliverables:**
- Confirm MVP scope and success metrics (retention, saves, ticket-link click-through)
- UX flows + clickable prototype for main journeys (feed → filters → detail → save)
- Initial Product Backlog (ordered) with acceptance criteria and release slicing
- Key decisions resolved:
  - Map provider (Mapbox vs Google)
  - Initial event providers (1–3 of Eventbrite/Ticketmaster/Meetup) and API access
  - Admin portal scope for MVP (minimal vs full)
  - Notification rules (reminders vs announcements)
- CI/CD baseline:
  - Backend deployed to staging
  - Mobile builds generated and distributed each sprint
- Revised delivery plan with best/likely/worst estimate and milestones

**Out of scope until explicitly agreed:** complex offline mode, multi-language, advanced roles/permissions, extensive scraping work, and any AI/ML-based recommendations.

---

## 5) Proposed Timeline (Ranges)

We recommend committing commercially to Sprint 0 first, then confirming the final plan. Based on the RFC MVP scope, a realistic delivery range is:

### Estimate (MVP)
- **Best-case:** **12 weeks** (Discovery + 5 build sprints) — minimal admin, 1 provider integration
- **Most-likely:** **14 weeks** (Discovery + 6 sprints) — 1–2 provider integrations, full MVP flows
- **Worst-case:** **16 weeks** (Discovery + 6 sprints + buffer) — integration issues, release iteration, scope growth

This assumes iOS + Android delivery via React Native (single codebase), backend in Go, and app store review time included as buffer.

---

## 6) Delivery Plan (2-Week Sprints)

Each Sprint has a **Sprint Goal**, ends with a **Sprint Review demo** and a test build (TestFlight + Android internal track), plus staging backend deployment.

### Sprint 1 — Foundations & MVP Skeleton
- Backend: API scaffold, auth (register/login/refresh), core data model/migrations, cities/categories
- Mobile: onboarding skeleton, auth screens, basic navigation, profile shell
- DevOps (light): staging environment baseline, logging/health checks

### Sprint 2 — Ingestion + Admin MVP
- Backend: ingestion worker for 1 provider (Eventbrite or Ticketmaster), normalization + storage
- Admin: minimal review queue + create/edit/approve/reject events
- Mobile: feed list (stubbed) + event detail page (first pass)

### Sprint 3 — Feed + Search/Filters + Map
- Backend: feed endpoints, filters, pagination, map endpoints (bbox/radius), caching for hot queries
- Mobile: feed + filters UI + map view + map interactions

### Sprint 4 — Onboarding + Preferences + Favorites
- Backend: preferences endpoints, save/unsave, saved list, 18+ gating
- Mobile: onboarding questionnaire finalized, edit preferences, favorites flows

### Sprint 5 — Notifications + Reporting + Privacy
- Backend: device registration, push notifications (saved reminders + announcements)
- Backend/Admin: event reporting + admin review; privacy export/deletion requests
- Mobile: notification settings, report flow, privacy request UI

### Sprint 6 — Stabilization + Launch Readiness
- Regression pass, performance checks (feed P95 target), security hardening
- Monitoring/alerts baseline, operational runbook (MVP-level)
- App store submission readiness (privacy labels, data disclosures) + rollout plan (staged release recommended)
- Data seeding: pre-populate events via ingestion for LA and Montreal before launch

### Sprint 7 — Buffer / App Store Iterations / Change Absorption (optional, as needed)
- Reserved for: provider integration issues, app store feedback, UX polish, backlog changes, stabilization

---

## 7) Commercials (Cost)

### Pricing Model
**Time & Materials per sprint**, with:
- fixed Sprint cadence and Sprint Review deliverables
- transparent burn (capacity, completed backlog items, remaining Product Backlog)
- optional **budget cap** per month/phase if needed for procurement

### Rates & Budget (Based on Provided Monthly Rates)
**Currency:** AED
**Sprint cost assumption:** 1 month = 4 weeks → 1 Sprint (2 weeks) = 0.5 month

| Role | Monthly Rate | Allocation | Effective Monthly Cost |
| --- | ---:| ---:| ---:|
| GoLang Developer | 10,000 | 1.0 | 10,000 |
| React Native Developer | 8,000 | 1.0 | 8,000 |
| Project Manager & Team Lead | 12,000 | 1.0 | 12,000 |
| UI/UX Designer | 7,000 | 0.4 | 2,800 |
| Admin Panel Developer (Nuxt/Next) | 8,000 | 0.5* | 4,000 |
| **Team Total** |  |  | **36,800** |

**Estimated cost per Sprint (2 weeks):** **18,400 AED** (Team Total × 0.5).

### MVP Scenario Cost Summary
- **Best-case (12 weeks / 6 Sprints):** **110,400 AED**
- **Most-likely (14 weeks / 7 Sprints):** **128,800 AED**
- **Worst-case (16 weeks / 8 Sprints):** **147,200 AED**

### Infrastructure Cost (Recurring)
- **$250–$400/month** — AWS (ECS Fargate, RDS PostgreSQL, ElastiCache Redis, S3, SES, CloudWatch, ALB).
- Billed separately from development costs; starts from Sprint 1 (staging) and continues post-launch.

**Excludes (unless agreed):** Mapbox/Google Maps billing, Apple/Google developer fees, any paid provider API tiers.

\*Allocation assumption: **0.5 FTE** (~2–3 days/week). Adjusting this changes Sprint and total costs linearly.

### Cost References (Market Context)
These are general market references for software development cost context in Dubai (not a substitute for the project’s agreed rates and delivery plan):
- [Appinventiv — Software development cost in Dubai](https://appinventiv.com/blog/software-development-cost-in-dubai/)
- [Binmile — Software development cost in Dubai](https://binmile.com/blog/software-development-cost-in-dubai)

### Payment Structure
- **Option A:** 20% kickoff, then monthly invoices based on actuals
- **Option B:** Fixed fee for Sprint 0, then T&M per sprint

---

## 8) Stakeholder Responsibilities (What We Need From You)

- Appoint a **Product Owner** (single decision-maker) for timely approvals and backlog prioritization
- Access to existing brand assets (logo, colors, typography) if any
- Access to required third-party systems (event provider APIs, credentials, sandbox accounts)
- Decision on map provider (Mapbox vs Google) and billing ownership
- App Store / Google Play developer accounts (or agreement on who owns/publishes)
  - Apple Developer Program ($99/year)
  - Google Play Developer ($25 one-time)
- Feedback during Sprint Reviews (and within 1–2 business days when decisions are needed)
- Escalation path defined for cases where decisions are delayed beyond 2 business days

---

## 9) Risks, Assumptions, and Mitigations

### Top Risks
| Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| Provider API coverage gaps | Medium | High | Start with 1 provider + manual admin entry; add 2nd provider early if needed |
| Map provider costs/limits | Medium | Medium | Decide Mapbox vs Google in Sprint 0; enforce usage limits + caching |
| Part-time resources become bottleneck | High | High | Designer must be 1 sprint ahead on designs; define backup plan for admin dev |
| API access not secured before Sprint 2 | Medium | Critical | Hard deadline in Sprint 0 with assigned owner |
| App store rejection | Medium | High | Include privacy labels, data disclosures in Sprint 6; buffer sprint reserved |
| Admin portal scope vs team | Medium | Medium | Keep admin minimal for MVP or add web capacity |
| Compliance expectations (CCPA/PIPEDA) | Low | High | Export/deletion flows + audit trail; validate with stakeholder early |
| Client decision latency | High | High | PO responds within 1–2 business days; escalation path defined in Sprint 0 |
| Quality near launch | Medium | High | Dedicated stabilization sprint + regression checklist |

### Key Assumptions (validate in Sprint 0)
- Supported platforms: iOS + Android (React Native)
- Authentication: email/password + reset/change password (via AWS SES for transactional email)
- Event providers: 1–3 (Eventbrite/Ticketmaster/Meetup) with API access provided
- Non-functional targets: feed performance P95 target, baseline uptime target, TLS + JWT, basic monitoring
- Launch regions: Los Angeles + Montreal (initial cities)

---

## 10) Scrum Reporting & Governance (Stakeholder Visibility)

### Sprint Review Summary (every 2 weeks)
- Increment demo (what is “Done”)
- Links to builds + release notes
- Updated Product Backlog priorities (what’s next)
- Decisions required from Product Owner

### Artifact Pack (what you get)
- Product backlog (prioritized, with acceptance criteria)
- Sprint plan and sprint-by-sprint deliverables
- Release notes per build
- Tech stack & architecture brief (services, environments, integrations, key decisions)

---

## 11) Next Steps

1. Confirm business goal, target users, and top 3 success metrics.
2. Approve Sprint 0 start date and nominate the Product Owner.
3. Complete Sprint 0 and sign off MVP scope + updated estimate.
4. Begin Scrum delivery with Sprint Reviews and continuous builds.

**Proposed Sprint 0 Start Date:** _[YYYY-MM-DD]_  
**Decision needed by:** _[YYYY-MM-DD]_ (to protect schedule)
