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
- **Web platform:** User access to feed, search, map, profile, and preferences on web.

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

### 10.4 Admin Front-End
- Admin portal framework: Nuxt.js (preferred) or Next.js.

### 10.5 Deployment on AWS
- Compute: ECS Fargate (API + worker services).
- Database: RDS PostgreSQL (automated backups; optional read replica).
- Cache: ElastiCache Redis.
- Object storage: S3.
- Networking: VPC + public/private subnets + ALB.
- Secrets: AWS Secrets Manager.
- Monitoring: CloudWatch logs/metrics + alarms; optional AWS X-Ray tracing.

## 11) API Surface (REST)
### 11.1 Auth
- `POST /v1/auth/register`
- `POST /v1/auth/login`
- `POST /v1/auth/logout`

### 11.2 User & Preferences
- `GET /v1/users/me`
- `PATCH /v1/users/me`
- `POST /v1/users/me/preferences`

### 11.3 Events
- `GET /v1/events` (filters: city, radius, category, date range, price, 18+)
- `GET /v1/events/{id}`
- `POST /v1/events/{id}/save`
- `DELETE /v1/events/{id}/save`

### 11.4 Map
- `GET /v1/events/map` (bbox or lat/lon + radius)

### 11.5 Admin
- `POST /v1/admin/events`
- `PATCH /v1/admin/events/{id}`
- `POST /v1/admin/events/{id}/approve`

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
### 13.1 Event
- `id` (UUID)
- `title` (string)
- `description` (text)
- `category` (enum)
- `start_time` / `end_time` (timestamp)
- `city` / `region` / `country` (string)
- `location_name` (string)
- `latitude` / `longitude` (float)
- `price_min` / `price_max` (decimal)
- `ticket_url` (string)
- `image_url` (string)
- `is_18_plus` (bool)
- `source` (enum: eventbrite, ticketmaster, meetup, manual)
- `status` (enum: pending, approved, rejected)
- `created_at` / `updated_at`

### 13.2 User
- `id` (UUID)
- `email` (string)
- `password_hash` (string)
- `name` (string)
- `city` (string)
- `radius_km` (int)
- `age_confirmed` (bool)
- `role` (enum: user, admin)
- `notification_opt_in` (bool)
- `data_export_requested_at` (timestamp, nullable)
- `deleted_at` (timestamp, nullable)
- `created_at` / `updated_at`

### 13.3 UserPreference
- `id` (UUID)
- `user_id` (UUID)
- `category` (enum)
- `created_at`

### 13.4 SavedEvent
- `id` (UUID)
- `user_id` (UUID)
- `event_id` (UUID)
- `created_at`

### 13.5 EventReport
- `id` (UUID)
- `user_id` (UUID)
- `event_id` (UUID)
- `reason` (string)
- `details` (text)
- `status` (enum: pending, reviewed, resolved)
- `created_at` / `updated_at`

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
- Coverage gaps if provider APIs are limited.
- Licensing requirements for images and text content.
- Ongoing costs for external API usage.

## 19) Milestones (Suggested)
1. **Week 1–2:** DB schema + ingestion pipeline (1 provider).
2. **Week 3–4:** Core API + event feed + filters.
3. **Week 5–6:** Mobile integration + onboarding + favorites.
4. **Week 7–8:** Admin portal + manual event entry.

## 20) Daily Plan (Single Full-Stack Engineer)


## 21) Acceptance Criteria (MVP)
- Users can browse and filter events for LA/Montreal.
- Users can view events on an interactive city map.
- Users can complete onboarding preferences.
- Users can save events and receive notifications for them.
- Admin can add, approve, and edit events.
- Events show ticket links; no ticket purchases in-app.
- All flows operate without AI/ML personalization.
