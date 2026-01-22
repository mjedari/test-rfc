# Sprint Plan (10 Working Days Each)

## Requirement Validation (Product + Tech Lead)

### Problem Statement
- Build a city-based event discovery platform with feed, filters, map, and event details for LA + Montreal, targeting students, without AI personalization.【F:RFC.md†L3-L6】【F:RFC.md†L19-L40】

### MVP Must-Haves
- Feed, search/filters, event detail, favorites, map, onboarding, auth, profile/preferences, reports, notifications, admin portal, and web access.【F:RFC.md†L23-L41】【F:RFC.md†L63-L78】【F:RFC.md†L182-L217】【F:RFC.md†L219-L231】

### Open Questions (Resolve by Sprint 1)
- Target mobile platform(s): iOS/Android/cross-platform.
- UI/UX baseline availability.
- API key access and rate limits for Eventbrite/Ticketmaster/Meetup.【F:RFC.md†L50-L55】
- Map provider choice (Mapbox vs Google).【F:RFC.md†L121-L126】
- Notification rules (reminders, city-wide announcements).【F:RFC.md†L38-L38】【F:RFC.md†L114-L114】
- Admin permission granularity and audit needs.【F:RFC.md†L97-L100】【F:RFC.md†L429-L432】

## Proposed Architecture (Tech Lead + DevOps)
- Go API service + Go ingestion worker + optional scraping worker.【F:RFC.md†L108-L113】
- PostgreSQL primary DB, Redis caching, S3-compatible storage for images/payloads.【F:RFC.md†L116-L119】【F:RFC.md†L134-L137】
- Admin portal in Nuxt.js/Next.js.【F:RFC.md†L144-L145】
- AWS ECS Fargate, RDS, ElastiCache, S3, Secrets Manager, CloudWatch.【F:RFC.md†L147-L154】【F:RFC.md†L413-L421】

## Sprint 1 — Foundations & MVP Skeleton
**Deliverable:** Running API + basic auth + minimal web UI + health checks.

### Backend
- Go API scaffold, JWT auth, users/me endpoints, cities/categories endpoints.【F:RFC.md†L156-L173】
- DB schema migrations for users, cities, categories.【F:RFC.md†L232-L346】

### Frontend (Web MVP shell)
- Web login/register + profile view/edit (minimal).

### DevOps
- CI pipeline (lint/test/build) + staging env (ECS + RDS dev).【F:RFC.md†L147-L154】

### QA
- Auth happy-path + negative cases; profile CRUD verification.

**Sprint 1 Acceptance**
- Users can register/login/logout and update profile basics.【F:RFC.md†L30-L33】【F:RFC.md†L163-L169】

## Sprint 2 — Event Ingestion & Admin MVP
**Deliverable:** One provider ingestion + admin CRUD for events.

### Backend
- Ingestion worker for one provider (Eventbrite or Ticketmaster).【F:RFC.md†L50-L55】【F:RFC.md†L109-L112】
- Normalize and store events + occurrences + venues.【F:RFC.md†L272-L295】
- Admin endpoints for event CRUD + approval workflow.【F:RFC.md†L198-L206】【F:RFC.md†L88-L90】

### Admin Web
- Event review queue + approve/reject + manual event create/edit.【F:RFC.md†L71-L77】【F:RFC.md†L219-L226】

### QA
- Validate ingestion correctness + approval workflow.

**Sprint 2 Acceptance**
- Admin can review/approve events and ingestion populates data.【F:RFC.md†L71-L77】【F:RFC.md†L402-L406】

## Sprint 3 — Event Feed + Search/Filters + Map API
**Deliverable:** User can browse/search/filter + map endpoints live.

### Backend
- Feed API with filters (city, radius, date, category, price, 18+).【F:RFC.md†L79-L83】【F:RFC.md†L175-L191】
- Map endpoints for bbox/radius queries.【F:RFC.md†L189-L191】
- Redis caching for hot feed queries.【F:RFC.md†L116-L119】

### Frontend
- Web feed list + filters + map view.

### QA
- Filter combinations, edge cases, and regression on approval flow.

**Sprint 3 Acceptance**
- Users can filter/search events and see map results.【F:RFC.md†L23-L29】【F:RFC.md†L189-L191】

## Sprint 4 — Onboarding + Preferences + Favorites
**Deliverable:** End-to-end onboarding + save flow.

### Backend
- Onboarding questionnaire + preferences endpoints.【F:RFC.md†L29-L33】【F:RFC.md†L163-L167】
- Favorites save/unsave and saved-events list.【F:RFC.md†L27-L27】【F:RFC.md†L182-L187】
- Age restriction gating (18+ flag + user confirmation).【F:RFC.md†L92-L95】

### Frontend
- Onboarding flow, preferences edit, save/unsave, saved list.

### QA
- Verify 18+ filtering + save notification toggles.

**Sprint 4 Acceptance**
- Onboarding and preferences affect feed; saved events persist.【F:RFC.md†L63-L69】【F:RFC.md†L182-L187】

## Sprint 5 — Notifications + Reporting + Privacy
**Deliverable:** Push notifications, reporting flow, privacy requests.

### Backend
- Device registration + push notifications (saved events + city announcements).【F:RFC.md†L38-L38】【F:RFC.md†L182-L187】
- Event reporting + admin review endpoints.【F:RFC.md†L35-L35】【F:RFC.md†L215-L216】
- Privacy request endpoints (export/deletion).【F:RFC.md†L193-L196】【F:RFC.md†L429-L432】

### Admin Web
- Notification composer + reports dashboard.【F:RFC.md†L230-L230】【F:RFC.md†L215-L216】

### QA
- Notification rules + privacy flow verification.

**Sprint 5 Acceptance**
- Notifications can be sent; reports and privacy requests function end-to-end.【F:RFC.md†L35-L38】【F:RFC.md†L193-L196】

## Sprint 6 — Stabilization, Compliance, Launch Readiness
**Deliverable:** Production-ready MVP (all flows stable).

### Tech Lead + DevOps
- Monitoring, alarms, health checks, logs.【F:RFC.md†L421-L421】
- Performance validation (P95 < 300ms).【F:RFC.md†L101-L103】
- Security hardening (TLS, secrets, access controls).【F:RFC.md†L105-L106】【F:RFC.md†L153-L154】

### QA
- Full regression, load baseline, data export/delete verification.

**Sprint 6 Acceptance**
- MVP acceptance criteria met; release candidate ready.【F:RFC.md†L471-L478】

## Risks & Mitigations
| Risk | Impact | Mitigation |
| --- | --- | --- |
| API coverage gaps | Incomplete feed | Add manual admin input + second provider early.【F:RFC.md†L56-L61】 |
| Map provider costs | Budget overrun | Choose Mapbox/Google with usage limits.【F:RFC.md†L121-L126】 |
| Notification failures | Poor retention | Retry/backoff + monitoring.【F:RFC.md†L114-L114】 |
| Compliance requirements | Legal exposure | Export/deletion endpoints + audit logs.【F:RFC.md†L429-L432】 |
