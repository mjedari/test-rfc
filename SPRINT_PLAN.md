# Sprint Plan (2-Week Sprints / 10 Working Days Each)

## Team Composition
| Role | Allocation |
| --- | --- |
| GoLang Developer | Full-time |
| React Native Developer | Full-time |
| Project Manager & Team Lead | Full-time |
| UI/UX Designer | Part-time (~40%) |
| Admin Panel Developer (Nuxt/Next) | Part-time (~50%) |

## Requirement Validation (Product + Tech Lead)

### Problem Statement
- Build a city-based event discovery mobile platform with feed, filters, map, and event details for LA + Montreal, targeting students, without AI personalization.

### MVP Must-Haves
- Mobile app: feed, search/filters, event detail, favorites, map, onboarding, auth, profile/preferences, push notifications.
- Admin portal (web): event review, manual entry, notification composer, reports dashboard.
- Backend: Go API, ingestion pipeline, push notifications (FCM/APNs), transactional email (AWS SES).

### Explicitly Out of Scope (MVP)
- Web user platform (deferred to Phase 2).
- AI/ML personalization or behavior-based recommendations.
- In-app ticket purchase.

### Open Questions (Resolve in Sprint 0)
- API key access and rate limits for Eventbrite/Ticketmaster/Meetup.
- Map provider choice (Mapbox vs Google) and billing ownership.
- Notification rules (reminders, city-wide announcements).
- Admin permission granularity and audit needs.
- App store account ownership (Apple Developer Program, Google Play).

## Proposed Architecture (Tech Lead + DevOps)
- Go API service + Go ingestion worker + optional scraping worker.
- PostgreSQL primary DB, Redis caching, S3-compatible storage for images/payloads.
- Admin portal in Nuxt.js/Next.js.
- Mobile app in React Native (iOS + Android).
- AWS ECS Fargate, RDS, ElastiCache, S3, SES, Secrets Manager, CloudWatch.
- Estimated infra cost: **$250–$400/month** (excludes map provider billing and paid API tiers).

## Critical Path
> Sprint 0 (design + decisions) → Sprint 1 (auth + API) → Sprint 2 (ingestion — external API dependency) → Sprint 3 (feed/map — depends on Sprint 2 data) → Sprint 6 (stabilization + app store submission)

**Highest-risk item:** External API integration in Sprint 2. If API access is not secured in Sprint 0, Sprint 2 slips and everything downstream shifts.

---

## Sprint 0 — Discovery & Setup (Required)
**Sprint Goal:** Finalize MVP scope, resolve key decisions, establish CI/CD baseline.

### Product + Design
- Confirm MVP scope and success metrics (retention, saves, ticket-link click-through).
- UX flows + clickable prototype for main journeys (feed → filters → detail → save).
- Initial Product Backlog (ordered) with acceptance criteria and release slicing.

### Tech Lead + DevOps
- Data model + API contract draft.
- Cloud architecture + CI/CD baseline (backend deployed to staging).
- Mobile build pipeline (TestFlight + Android internal track).

### Key Decisions (must resolve before Sprint 1)
- Map provider (Mapbox vs Google) and billing ownership.
- Event providers (1–3 of Eventbrite/Ticketmaster/Meetup) — API access secured.
- Admin portal scope for MVP (minimal vs full).
- Notification rules (reminders vs announcements).
- App store account ownership.

**Sprint 0 Acceptance**
- Product Backlog exists with acceptance criteria.
- Staging environment deployed with health checks passing.
- Mobile build pipeline producing test builds.
- All key decisions documented and agreed.

---

## Sprint 1 — Foundations & MVP Skeleton
**Sprint Goal:** Running API + basic auth + mobile shell + health checks.

### Backend
- Go API scaffold, JWT auth, users/me endpoints, cities/categories endpoints.
- DB schema migrations for users, cities, categories.
- AWS SES integration for password reset emails.

### Mobile (React Native)
- Onboarding skeleton, auth screens (register/login), basic navigation, profile shell.

### DevOps
- CI pipeline (lint/test/build) + staging env (ECS + RDS dev).

### QA
- Auth happy-path + negative cases; profile CRUD verification.

**Sprint 1 Acceptance**
- Users can register/login/logout and update profile basics on mobile.
- Password reset email sends via AWS SES.

**Design lead time:** Designer delivers Sprint 2 screens (event list, event detail, admin queue) during this sprint.

---

## Sprint 2 — Event Ingestion & Admin MVP
**Sprint Goal:** One provider ingestion + admin CRUD for events.

### Backend
- Ingestion worker for one provider (Eventbrite or Ticketmaster).
- Normalize and store events + occurrences + venues.
- Admin endpoints for event CRUD + approval workflow.

### Admin Web
- Event review queue + approve/reject + manual event create/edit.

### Mobile
- Feed list (stubbed with real data from ingestion) + event detail page (first pass).

### QA
- Validate ingestion correctness + approval workflow.

**Sprint 2 Acceptance**
- Admin can review/approve events; ingestion populates data from at least 1 provider.
- Mobile shows event list with real ingested data.

**Design lead time:** Designer delivers Sprint 3 screens (filters, map view, search) during this sprint.

---

## Sprint 3 — Event Feed + Search/Filters + Map API
**Sprint Goal:** User can browse/search/filter + map endpoints live.

### Backend
- Feed API with filters (city, radius, date, category, price, 18+).
- Map endpoints for bbox/radius queries.
- Redis caching for hot feed queries.
- Optional: second provider integration.

### Mobile
- Feed + filters UI + map view + map interactions.

### QA
- Filter combinations, edge cases, and regression on approval flow.

**Sprint 3 Acceptance**
- Users can filter/search events and see map results on mobile.

---

## Sprint 4 — Onboarding + Preferences + Favorites
**Sprint Goal:** End-to-end onboarding + save flow.

### Backend
- Onboarding questionnaire + preferences endpoints.
- Favorites save/unsave and saved-events list.
- Age restriction gating (18+ flag + user confirmation).

### Mobile
- Onboarding flow, preferences edit, save/unsave, saved list.

### QA
- Verify 18+ filtering + save notification toggles.

**Sprint 4 Acceptance**
- Onboarding and preferences affect feed; saved events persist.

---

## Sprint 5 — Notifications + Reporting + Privacy
**Sprint Goal:** Push notifications, reporting flow, privacy requests.

### Backend
- Device registration + push notifications via FCM/APNs (saved events + city announcements).
- Event reporting + admin review endpoints.
- Privacy request endpoints (export/deletion).

### Admin Web
- Notification composer + reports dashboard.

### Mobile
- Notification settings, report flow, privacy request UI.

### QA
- Notification delivery verification + privacy flow end-to-end.

**Sprint 5 Acceptance**
- Notifications can be sent and received on device.
- Reports and privacy requests function end-to-end.

---

## Sprint 6 — Stabilization, Compliance, Launch Readiness
**Sprint Goal:** Production-ready MVP (all flows stable).

### Tech Lead + DevOps
- Monitoring, alarms, health checks, logs.
- Performance validation (P95 < 300ms).
- Security hardening (TLS, secrets, access controls).

### Mobile
- App store submission readiness (privacy labels, data collection disclosures, screenshots).

### QA
- Full regression, load baseline, data export/delete verification.

### Data Seeding
- Pre-populate events via ingestion runs for both LA and Montreal before launch.

**Sprint 6 Acceptance**
- MVP acceptance criteria met; release candidate ready.
- App submitted to App Store and Google Play.

---

## Sprint 7 — Buffer (as needed)
- Reserved for: app store review feedback, provider integration issues, UX polish, backlog changes, stabilization.

---

## Risks & Mitigations
| Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| API coverage gaps | Medium | High | Add manual admin input + second provider early. |
| Map provider costs | Medium | Medium | Choose Mapbox/Google in Sprint 0 with usage limits. |
| Part-time resources bottleneck | High | High | Designer 1 sprint ahead; backup plan for admin dev. |
| API access not secured before Sprint 2 | Medium | Critical | Hard deadline in Sprint 0; owner assigned. |
| App store rejection | Medium | High | Privacy labels + data disclosures in Sprint 6. |
| Notification failures | Medium | Medium | Retry/backoff + monitoring. |
| Compliance requirements | Low | High | Export/deletion endpoints + audit logs. |
| Client decision latency | High | High | PO responds within 1–2 days; escalation path defined. |
