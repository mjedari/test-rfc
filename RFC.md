# RCF: Event Discovery Mobile Application (Go)

## 1) Overview
**Goal:** Build a mobile-backed event discovery platform that aggregates local events for a user’s city and presents them in a clean, reliable, and easy-to-use feed. The MVP focuses on Los Angeles and Montreal and serves primarily students, while remaining extensible for broader demographics later.

**Out of scope (explicitly excluded):** AI/ML-driven personalization, recommendation models, or behavior-based ranking beyond deterministic rules.

## 2) Product Objectives (MVP)
- Provide a single, centralized feed of local events.
- Offer a simple, fast UI experience with strong reliability.
- Support onboarding questionnaires to seed user preferences.
- Enable event saving/favorites and event detail views with ticket links.
- Support 18+ event handling and basic compliance requirements (CCPA, PIPEDA).

**Success metrics:**
- Users return regularly (D7/D30 retention signals).
- Users save events and click through to details/ticket links.

## 3) Target Launch Regions
- Los Angeles, CA, USA
- Montreal, QC, Canada

## 4) Core Features (MVP)
- **Event feed:** Events within a chosen radius and city.
- **Search & filters:** Category, date, location, price.
- **Event details:** Title, time, location, description, photos, price, ticket link.
- **Favorites:** Save/unsave events.
- **Map view:** Interactive city map with events plotted geographically.
- **User onboarding:** Preference questionnaire (category/interest selection).
- **Authentication:** Sign up, login, logout.
- **User profile:** View/edit profile details (name, city).
- **Preferences management:** Edit onboarding preferences post-signup.
- **Password management:** Change password and reset password flow.
- **Settings & privacy:** Notification preferences, data export/deletion.
- **Event reporting:** Allow users to flag incorrect or outdated events.
- **City/location switching:** Change city and radius post-onboarding.
- **Provider attribution:** Display event source on details.
- **Push notifications:** Reminders and updates (limited to saved events and city-wide announcements).
- **Admin portal:** Event review and manual input to fill gaps.

**Explicitly deferred to Phase 2:** Web user platform (user access to feed, search, map on web).

## 5) Event Categories (MVP Required)
- Concerts / live music
- Sporting events
- Campus & university events
- Festivals

**Additional categories supported in data model (for later expansion):** nightlife, food & drink, social activities, fitness & wellness, community events, arts & culture, pop-ups, family-friendly.

## 6) Data Sources & Integrations
**MVP integrations (1–3 APIs):**
- Eventbrite
- Ticketmaster
- Meetup

**Manual data entry:**
- Admin-side form for campus or local events.

**Notes:**
- If API coverage is insufficient, targeted scraping may be used where permitted by provider terms and robots.txt.
- Any provider-specific contact/contract work is out of scope.

## 7) Functional Requirements
### 7.1 User Flow
1. User downloads app and selects city.
2. User completes onboarding questionnaire (category interests).
3. User sees personalized feed based on city + questionnaire (no behavioral AI).
4. User can filter, search, save events, and open details.
5. User can view a map of events.

### 7.2 Admin Flow (Web)
1. Admin logs in to the admin portal.
2. Admin reviews event ingestion queue (pending/flagged events).
3. Admin edits or approves events; rejects invalid entries.
4. Admin manages categories/sources and updates featured flags.
5. Admin previews feeds and map views for QA.
6. Admin sends notifications for city-wide announcements or saved-event reminders.

### 7.3 Event Feed Rules
- Base set: city + radius + date range (default: next 14 days).
- Sorting: chronological by start time.
- Optional secondary sort: featured/promoted flag (admin-managed).
- Filter by category, date, price range, and 18+.

### 7.4 Ticketing
- Provide external links to ticketing providers; no in-app purchase.

### 7.5 Content Moderation
- Admin approval for manual events.
- Validation for required fields (title, time, city, location).

### 7.6 Age Restriction
- Event-level 18+ flag.
- User must confirm age during onboarding.
- If user < 18, 18+ events are hidden.

### 7.7 User Roles
- **User:** Standard account for browsing, saving events, editing profile/preferences, and managing notification settings.
- **Admin:** Elevated access to approve/edit events, manage sources/categories, and send announcements.

## 8) Non-Functional Requirements
- **Performance:** feed response < 300ms at P95.
- **Reliability:** 99.5% API uptime target for MVP.
- **Scalability:** support 10,000 users with headroom for multiple cities.
- **Security:** OAuth2/JWT auth; encryption at rest; TLS in transit.
- **Compliance:** CCPA (data deletion, access), PIPEDA (consent, data usage).

## 9) System Architecture (Go)
### 9.1 Services
- **API Service (Go):** REST API for mobile app.
- **Ingestion Worker (Go):** Scheduled ETL from external APIs.
- **Scraping Worker (Go):** Targeted scraping jobs when APIs are insufficient.
- **Admin Portal (Web, minimal):** CRUD for events and providers.
- **Notification Service:** Push notifications for saved events.

### 9.2 Storage
- **Primary DB:** PostgreSQL
- **Caching:** Redis for feed results and common queries
- **Object storage:** S3-compatible for images

### 9.3 External Services
- Eventbrite API
- Ticketmaster API
- Meetup API
- Map provider (Mapbox or Google Maps)
- Push notifications (Firebase Cloud Messaging / APNs)
- Transactional email (AWS SES)

## 10) Technology Stack
### 10.1 Backend & Services
- Go for API service and worker services (ingestion, scraping, notifications).
- REST API surface for mobile and admin clients.
- Auth: JWT (aligned with OAuth2/JWT note in non-functional requirements).

### 10.2 Data & Storage
- PostgreSQL as the primary database.
- Redis for caching feeds/common queries and rate limiting.
- S3-compatible object storage for images and raw provider payloads.

### 10.3 External Integrations
- Event providers: Eventbrite, Ticketmaster, Meetup.
- Maps: Mapbox or Google Maps.
- Push notifications: Firebase Cloud Messaging (FCM) / APNs.
- Transactional email: AWS SES (password reset, account verification).

### 10.4 Mobile
- React Native (iOS + Android from a single codebase).

### 10.5 Admin Front-End
- Admin portal framework: Nuxt.js (preferred) or Next.js.

### 10.6 Deployment on AWS
- Compute: ECS Fargate (API + worker services).
- Database: RDS PostgreSQL (automated backups; optional read replica).
- Cache: ElastiCache Redis.
- Object storage: S3.
- Networking: VPC + public/private subnets + ALB.
- Secrets: AWS Secrets Manager.
- Email: AWS SES for transactional email (password reset, account verification).
- Monitoring: CloudWatch logs/metrics + alarms; optional AWS X-Ray tracing.

### 10.7 Estimated Infrastructure Cost
- **$250–$400/month** (ECS Fargate, RDS, ElastiCache, S3, SES, CloudWatch, ALB).
- Excludes map provider billing (Mapbox or Google Maps) and paid event API tiers.

## 11) API Surface (REST)
### 11.1 Auth
- `POST /v1/auth/register`
- `POST /v1/auth/login`
- `POST /v1/auth/logout`
- `POST /v1/auth/refresh`

### 11.2 User & Preferences
- `GET /v1/users/me`
- `PATCH /v1/users/me`
- `PUT /v1/users/me/preferences`
- `GET /v1/users/me/preferences`
- `POST /v1/users/me/devices`
- `DELETE /v1/users/me/devices/{id}`

### 11.3 Cities & Categories
- `GET /v1/cities`
- `GET /v1/categories`

### 11.4 Events & Occurrences
- `GET /v1/events` (filters: city, radius, category, date range, price, 18+)
- `GET /v1/events/{id}`
- `GET /v1/events/{id}/occurrences`
- `GET /v1/occurrences` (filters: city, radius, category, date range, price, 18+)
- `GET /v1/occurrences/{id}`

### 11.5 Saved Events & Notifications
- `POST /v1/events/{id}/save`
- `DELETE /v1/events/{id}/save`
- `PATCH /v1/events/{id}/save` (toggle `notify_enabled`)
- `GET /v1/users/me/saved-events`
- `GET /v1/users/me/notifications`

### 11.6 Map
- `GET /v1/events/map` (bbox or lat/lon + radius)
- `GET /v1/occurrences/map` (bbox or lat/lon + radius)

### 11.7 Reports & Privacy
- `POST /v1/events/{id}/reports`
- `GET /v1/users/me/privacy/requests`
- `POST /v1/users/me/privacy/requests`

### 11.8 Admin
- `GET /v1/admin/events` (filters: status, source, city, date range)
- `POST /v1/admin/events`
- `PATCH /v1/admin/events/{id}`
- `POST /v1/admin/events/{id}/approve`
- `POST /v1/admin/events/{id}/reject`
- `GET /v1/admin/occurrences` (filters: city, date range)
- `POST /v1/admin/occurrences`
- `PATCH /v1/admin/occurrences/{id}`
- `GET /v1/admin/categories`
- `POST /v1/admin/categories`
- `PATCH /v1/admin/categories/{id}`
- `GET /v1/admin/sources`
- `POST /v1/admin/sources`
- `PATCH /v1/admin/sources/{id}`
- `POST /v1/admin/notifications`
- `GET /v1/admin/notifications`
- `GET /v1/admin/reports`
- `PATCH /v1/admin/reports/{id}`
- `GET /v1/admin/audit-logs`

## 12) Front-End (Admin Panel)
**Framework:** Nuxt.js (preferred) or Next.js.

**Key screens and capabilities:**
- **Admin dashboard:** Event review queue, status overview, basic analytics.
- **Event management:** Create/edit/approve/reject events, manage categories, manage sources.
- **Feed preview:** View the home feed as users see it (radius, city, filters).
- **Search & filtering:** Category, date, location, price.
- **Event detail pages:** Photos, descriptions, ticket links, age restriction flags.
- **Interactive map:** City map with plotted events and map-based filtering.
- **User support:** View user profiles and preferences for troubleshooting.
- **Notifications:** Compose and send push notifications for announcements and saved-event reminders.

## 13) Data Model (Initial)
### 13.1 City
- `id` (UUID)
- `name` (string)
- `region` (string)
- `country` (string)
- `timezone` (string)
- `latitude` / `longitude` (float)
- `default_radius_km` (int)
- `is_active` (bool)
- `created_at` / `updated_at`

### 13.2 Venue
- `id` (UUID)
- `name` (string)
- `address_line1` / `address_line2` (string, nullable)
- `city_id` (UUID)
- `region` / `postal_code` / `country` (string)
- `latitude` / `longitude` (float)
- `timezone` (string)
- `phone` (string, nullable)
- `website_url` (string, nullable)
- `created_at` / `updated_at`

### 13.3 EventSource
- `id` (UUID)
- `name` (string)
- `type` (enum: api, manual, scrape)
- `attribution_text` (string, nullable)
- `terms_url` (string, nullable)
- `is_active` (bool)
- `created_at` / `updated_at`

### 13.4 EventCategory
- `id` (UUID)
- `slug` (string, unique)
- `name` (string)
- `is_active` (bool)
- `created_at` / `updated_at`

### 13.5 Event
- `id` (UUID)
- `title` (string)
- `description` (text)
- `status` (enum: pending, approved, rejected)
- `is_18_plus` (bool)
- `price_min` / `price_max` (decimal, nullable)
- `currency` (string)
- `ticket_url` (string, nullable)
- `source_id` (UUID)
- `source_event_id` (string)
- `source_url` (string, nullable)
- `organizer_name` (string, nullable)
- `created_at` / `updated_at`

### 13.6 EventOccurrence
- `id` (UUID)
- `event_id` (UUID)
- `venue_id` (UUID, nullable for online events)
- `city_id` (UUID)
- `start_time` / `end_time` (timestamp)
- `timezone` (string)
- `is_online` (bool)
- `created_at` / `updated_at`

### 13.7 EventCategoryMap
- `id` (UUID)
- `event_id` (UUID)
- `category_id` (UUID)
- `created_at`

### 13.8 EventImage
- `id` (UUID)
- `event_id` (UUID)
- `url` (string)
- `width` / `height` (int, nullable)
- `is_primary` (bool)
- `source` (string)
- `created_at`

### 13.9 IngestionRun
- `id` (UUID)
- `source_id` (UUID)
- `started_at` / `finished_at` (timestamp)
- `status` (enum: success, partial, failed)
- `events_fetched` / `events_created` / `events_updated` (int)
- `error_summary` (text, nullable)

### 13.10 RawProviderPayload
- `id` (UUID)
- `source_id` (UUID)
- `source_event_id` (string)
- `payload_json` (jsonb)
- `checksum` (string)
- `fetched_at` (timestamp)

### 13.11 User
- `id` (UUID)
- `email` (string)
- `password_hash` (string)
- `name` (string)
- `city_id` (UUID)
- `radius_km` (int)
- `age_confirmed_at` (timestamp, nullable)
- `role` (enum: user, admin)
- `notification_opt_in` (bool)
- `last_login_at` (timestamp, nullable)
- `status` (enum: active, suspended, deleted)
- `created_at` / `updated_at`

### 13.12 UserPreference
- `id` (UUID)
- `user_id` (UUID)
- `category_id` (UUID)
- `created_at`

### 13.13 SavedEvent
- `id` (UUID)
- `user_id` (UUID)
- `event_id` (UUID)
- `notify_enabled` (bool)
- `created_at`

### 13.14 UserDevice
- `id` (UUID)
- `user_id` (UUID)
- `platform` (enum: ios, android, web)
- `push_token` (string)
- `last_seen_at` (timestamp, nullable)
- `created_at` / `updated_at`

### 13.15 Notification
- `id` (UUID)
- `type` (enum: saved_event_reminder, city_announcement)
- `title` / `body` (string)
- `city_id` (UUID, nullable)
- `event_id` (UUID, nullable)
- `scheduled_at` / `sent_at` (timestamp, nullable)
- `status` (enum: draft, scheduled, sent, failed)
- `created_by_admin_id` (UUID, nullable)
- `created_at` / `updated_at`

### 13.16 EventReport
- `id` (UUID)
- `user_id` (UUID)
- `event_id` (UUID)
- `reason` (string)
- `details` (text, nullable)
- `status` (enum: pending, reviewed, resolved)
- `reviewed_by_admin_id` (UUID, nullable)
- `resolved_at` (timestamp, nullable)
- `created_at` / `updated_at`

### 13.17 PrivacyRequest
- `id` (UUID)
- `user_id` (UUID)
- `type` (enum: export, deletion)
- `status` (enum: requested, in_progress, completed, rejected)
- `requested_at` / `completed_at` (timestamp, nullable)
- `created_at` / `updated_at`

### 13.18 AdminAuditLog
- `id` (UUID)
- `admin_user_id` (UUID)
- `action` (string)
- `entity_type` (string)
- `entity_id` (UUID)
- `metadata_json` (jsonb, nullable)
- `created_at`

## 14) Ingestion & Normalization
- Scheduled ingestion jobs every 30–60 minutes.
- Normalize incoming events to internal schema.
- Dedupe by: (title + start_time + venue + city) with fuzzy matching thresholds.
- Store raw provider payloads for debugging (in a separate table or object store).

## 15) Scraping Approach (MVP Extension)
- Scraping is used only when API coverage is insufficient and only where permitted by provider terms.
- Implement rate limiting, caching, and backoff to avoid overloading sources.
- Store fetch metadata (source URL, fetched_at) for auditing and troubleshooting.

## 16) Deployment (AWS)
### 16.1 Infrastructure
- **Compute:** ECS Fargate for API and worker services.
- **Database:** RDS PostgreSQL with automated backups and read replica optional for scale.
- **Cache:** ElastiCache Redis for feed caching and rate limiting.
- **Object storage:** S3 for images and raw provider payloads.
- **Networking:** VPC with public/private subnets, ALB for API routing.
- **Secrets:** AWS Secrets Manager for API keys and DB credentials.
- **Monitoring:** CloudWatch logs/metrics + alarms; optional X-Ray tracing.

### 16.2 Scaling Target (10,000 users)
- Target 2–4 API tasks minimum with autoscaling on CPU/RPS.
- Redis caching for hot feeds and common queries.
- Use connection pooling and pagination to keep DB load stable.
- Background ingestion/scraping isolated in separate worker services.

## 17) Privacy & Compliance
- Provide data export and deletion endpoints.
- Track consent timestamp in user profile.
- Maintain audit logs for admin actions.

## 18) Risks & Open Questions
| Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| Provider API coverage gaps | Medium | High | Start with 1 provider + manual admin entry; add 2nd provider early if needed |
| Map provider costs/limits | Medium | Medium | Decide Mapbox vs Google in Sprint 0; enforce usage limits + caching |
| Part-time resources become bottleneck | High | High | Designer must be 1 sprint ahead on designs; define backup plan for admin dev |
| API access not secured before Sprint 2 | Medium | Critical | Assign API key acquisition to Sprint 0 with a hard deadline |
| App store rejection | Medium | High | Include privacy labels, data disclosures, and review guideline compliance in Sprint 6 |
| Client decision latency | High | High | Product Owner must respond within 1–2 business days; escalation path defined in Sprint 0 |
| Licensing requirements for images/text | Low | Medium | Review provider terms in Sprint 0; store attribution data |
| Ongoing costs for external API usage | Low | Medium | Monitor usage; enforce rate limits and caching |

## 19) Team Composition
| Role | Allocation | Primary Responsibilities |
| --- | --- | --- |
| GoLang Developer | Full-time | Backend APIs, data model, integrations, performance, security |
| React Native Developer | Full-time | Mobile app (iOS/Android), UI implementation, state management |
| Project Manager & Team Lead | Full-time | Delivery planning, Scrum, stakeholder comms, release coordination |
| UI/UX Designer | Part-time (~40%) | UX flows, wireframes, UI kit, prototypes, handoff specs |
| Admin Panel Developer (Nuxt/Next) | Part-time (~50%) | Admin portal UI (review queue, CRUD, approvals) |

## 20) Milestones (Suggested)
**Assumes the team composition above. Estimates include testing, bug fixing, and release hardening. All sprints are 2 weeks (10 working days).**

### Critical Path
> Sprint 0 (design + decisions) → Sprint 1 (auth + API) → Sprint 2 (ingestion — external API dependency) → Sprint 3 (feed/map — depends on Sprint 2 data) → Sprint 6 (stabilization + app store submission)

0. **Sprint 0 — Weeks 1–2: Discovery + Setup**
   - Finalize MVP scope, success metrics, and user flows
   - UX flows + clickable prototype for main journeys
   - Data model + API contract draft
   - Cloud architecture + CI/CD baseline (staging deployed)
   - Key decisions resolved: map provider, event providers (API access secured), admin portal MVP scope
   - Initial Product Backlog (ordered) with acceptance criteria
1. **Sprint 1 — Weeks 3–4: Foundations & MVP Skeleton**
   - Backend: API scaffold, JWT auth, users/me, cities/categories endpoints
   - Mobile: onboarding skeleton, auth screens, basic navigation, profile shell
   - DevOps: staging environment baseline, logging/health checks
   - DB schema migrations for users, cities, categories
2. **Sprint 2 — Weeks 5–6: Ingestion + Admin MVP**
   - Backend: ingestion worker for 1 provider (Eventbrite or Ticketmaster), normalization + storage
   - Admin: minimal review queue + create/edit/approve/reject events
   - Mobile: feed list (stubbed) + event detail page (first pass)
3. **Sprint 3 — Weeks 7–8: Feed + Search/Filters + Map**
   - Backend: feed endpoints, filters, pagination, map endpoints (bbox/radius), Redis caching
   - Mobile: feed + filters UI + map view + map interactions
   - Optional: second provider integration
4. **Sprint 4 — Weeks 9–10: Onboarding + Preferences + Favorites**
   - Backend: preferences endpoints, save/unsave, saved list, 18+ gating
   - Mobile: onboarding questionnaire finalized, edit preferences, favorites flows
5. **Sprint 5 — Weeks 11–12: Notifications + Reporting + Privacy**
   - Backend: device registration, push notifications (FCM/APNs), event reporting, privacy export/deletion
   - Admin: notification composer + reports dashboard
   - Mobile: notification settings, report flow, privacy request UI
6. **Sprint 6 — Weeks 13–14: Stabilization + Launch Readiness**
   - Regression testing, performance checks (feed P95 < 300ms), security hardening
   - Monitoring/alerts baseline, operational runbook
   - App store submission readiness (privacy labels, data disclosures)
   - Data seeding plan: pre-populate events via ingestion before launch
7. **Sprint 7 — Weeks 15–16: Buffer (as needed)**
   - Reserved for: provider integration issues, app store review feedback, UX polish, scope changes

## 21) Acceptance Criteria (MVP)
- Users can browse and filter events for LA/Montreal.
- Users can view events on an interactive city map.
- Users can complete onboarding preferences.
- Users can save events and receive notifications for them.
- Admin can add, approve, and edit events.
- Events show ticket links; no ticket purchases in-app.
- All flows operate without AI/ML personalization.
