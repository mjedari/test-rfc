# Event Discovery Platform — Stakeholder Report

## Executive Summary
This report outlines the delivery approach for the event discovery platform described in the RFC, including scope, timeline, cost, responsibilities, and a sprint-based deployment plan. The MVP targets Los Angeles and Montreal, delivering a centralized event feed with search, map, favorites, onboarding, and admin tooling, without AI/ML personalization.【F:RFC.md†L3-L41】

## Project Scope (MVP)
- Event feed, search/filters, map view, event detail pages, favorites, onboarding, and user profile/preferences.【F:RFC.md†L23-L41】
- Admin portal for event review, manual entry, and notifications.【F:RFC.md†L71-L77】【F:RFC.md†L219-L231】
- Privacy/export and deletion flows to meet compliance needs.【F:RFC.md†L429-L432】

## Commercials
- **Cost:** 20,000 AED
- **Development Duration:** **12–16 weeks (3–4 months)** depending on scope
- **Infra Cost:** **$250–$400/month**

## Responsibilities
- Front-end development
- Back-end services development
- Deployment of all services on AWS
- Coordination between team members

## Deployment Plan (2-week sprints)

### Sprint 1 (Weeks 1–2): Foundations
- API scaffold, auth, core data models, CI/CD setup, initial environment provisioning.
- Deliverable: baseline API + auth and environment ready for further development.

### Sprint 2 (Weeks 3–4): Ingestion + Admin MVP
- Provider ingestion for one API, normalization, and admin event approval workflow.
- Deliverable: ingested events visible in admin review queue.

### Sprint 3 (Weeks 5–6): Feed + Search + Map APIs
- Event feed, filters, pagination, and map endpoints with caching.
- Deliverable: functional event feed and map endpoints.

### Sprint 4 (Weeks 7–8): Onboarding + Favorites
- Preferences onboarding, save/unsave flow, saved-events list, 18+ gating.
- Deliverable: onboarding-driven feed and favorites.

### Sprint 5 (Weeks 9–10): Notifications + Reporting + Privacy
- Push notifications, event reporting, and privacy request flows.
- Deliverable: notifications and compliance workflows in place.

### Sprint 6 (Weeks 11–12): Stabilization + Launch Readiness
- Regression testing, performance checks, security hardening, monitoring/alerts.
- Deliverable: MVP release candidate.

### Buffer (Weeks 13–16): Scope-dependent Enhancements
- Reserved for additional provider integrations, UX polish, and change requests.

## Risks & Assumptions (Summary)
- Provider API coverage may require manual input or additional integrations.【F:RFC.md†L50-L61】
- Map provider costs and usage limits must be finalized early.【F:RFC.md†L121-L126】
- Compliance requirements are included but need final legal review.【F:RFC.md†L429-L432】
