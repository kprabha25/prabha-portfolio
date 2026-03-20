# Freelance Project: Construction Site Field Operations Platform — CoatOps

> **Confidentiality Note:** Client name is withheld per standard freelance NDA. Full technical details, architecture decisions, and code samples are available for discussion in interviews.

---

## Project Overview

Designed and built **CoatOps**, a production-grade full-stack field operations platform for managing painting and coating works across construction sites — as the **sole developer**. The client needed a custom solution to replace manual paper-based workflows covering worker attendance, material inventory, tool management, and real-time progress reporting across multiple active sites.

The system was delivered as a **Progressive Web App (PWA)** to support field workers who operate in areas with unreliable internet connectivity.

---

## My Role

**Freelance Full-Stack Developer** (sole engineer)

- Gathered requirements from the client and translated them into a full technical specification
- Architected the complete system from scratch — database schema, REST API design, frontend architecture, offline strategy
- Implemented all 18 backend domain modules and the full React frontend (42 pages)
- Designed and implemented the offline-first sync mechanism for field use
- Built async worker infrastructure (RabbitMQ) for notifications and audit logging
- Integrated all third-party services (AWS SES, S3, Twilio, Firebase, Google OAuth)
- Deployed the application to AWS EC2 with PM2 process management and Nginx reverse proxy
- Set up GitHub Actions CI/CD pipeline for zero-downtime automated deployments
- Wrote comprehensive technical documentation for client handoff

---

## Tech Stack

| Category | Technologies |
|---|---|
| **Runtime / Framework** | Node.js 18+, Express.js 4, JavaScript |
| **Frontend** | React 18, Vite 6, Ant Design 6, React Router 7 |
| **State Management** | Zustand 5, TanStack Query 5 |
| **Database** | MongoDB 7 (Replica Set), Mongoose ODM, Redis 7 |
| **Message Queue** | RabbitMQ 3 |
| **Authentication** | JWT (access + refresh tokens), Passport.js, Google OAuth 2.0 |
| **Cloud / Storage** | AWS S3 (file uploads), AWS SES (email) |
| **Notifications** | Firebase Cloud Messaging (FCM), Twilio (SMS/OTP) |
| **PWA / Offline** | Workbox service workers, IndexedDB (idb), offline-first sync |
| **Security** | Helmet, XSS-Clean, bcrypt, express-rate-limit, CORS |
| **Export** | jsPDF, jspdf-autotable, PapaParse (PDF + CSV reports) |
| **Charts** | Recharts |
| **DevOps** | Docker Compose (dev), PM2 (prod), AWS EC2, Nginx, GitHub Actions |
| **API Docs** | Swagger / OpenAPI (116 endpoints) |
| **Logging** | Winston, Morgan |
| **i18n** | i18next (English + Arabic) |

---

## Key Technical Contributions

### 1. Offline-First PWA for Field Workers
- Built a full offline attendance entry flow using IndexedDB (via `idb`) — field workers on sites with no connectivity enter attendance locally
- `useOfflineSync` custom hook detects reconnection via `navigator.onLine` and auto-syncs queued records to the server
- UUID-based `offlineId` prevents any duplicate submissions on re-sync — server-side deduplication check on every sync
- Workbox service workers cache the app shell so the PWA loads and functions without internet

### 2. Hierarchical Site Management
- Designed a flexible site hierarchy: **Site → Block/Tower → Floor → Flat → Room**
- Each flat has an independent `FlatProgress` document tracking stage-by-stage completion for three ceiling/wall types
- Concrete ceiling (11 stages), gypsum ceiling (7 stages), and walls (6 stages) — each with completion tracking and percentage calculation

### 3. 23-Type Painting Activity System
- Modeled 23 distinct painting activity types across ceiling and wall categories
- Each attendance record captures activity type, time range (auto-calculates hours worked), and a 1–5 quality rating per activity
- Foremen can enter bulk attendance for their team from a single form

### 4. Async Notification Pipeline (RabbitMQ)
- Designed a message queue architecture with 4 dedicated workers:
  - **Email Worker** — AWS SES transactional emails
  - **SMS Worker** — Twilio OTP and alerts
  - **Push Worker** — Firebase FCM mobile push notifications
  - **Audit Worker** — Immutable action logging
- 3-retry dead-letter queue (DLQ) strategy: failed messages retry 3 times before routing to `{queue}_dlq` for manual inspection
- API returns immediately; workers process asynchronously — notification failures never affect the user-facing response

### 5. Role-Based Access Control (RBAC)
- 4 roles: superadmin, admin, foreman, labour
- 40+ granular permissions enforced at the route level via `rbac.js` middleware
- Each role can only access endpoints and data scopes relevant to their function

### 6. JWT Auth with Token Rotation
- Access tokens (15 min) + refresh tokens (7 days) — short-lived access for security, refresh for seamless UX
- Redis token blacklist for immediate logout invalidation
- Google OAuth 2.0 as an alternative login method
- Frontend Axios interceptors auto-refresh expired tokens and retry queued requests — transparent to the user

### 7. Analytics & Reporting
- Productivity dashboard: hours worked, quality ratings, activity breakdowns per employee per site
- Manpower analytics: daily workforce trends, utilization rates
- PDF and CSV report export (jsPDF + PapaParse): attendance, payroll summary, material consumption, tool movements

### 8. Material Inventory & Tool Registry
- Material stock tracking with low-stock alert threshold per site
- Inter-site material and tool transfers with full transfer history
- Tool status lifecycle: available → in-use → maintenance → lost
- Tool assignment to specific employees with serial number tracking

### 9. Modular Domain-Driven Architecture
- 18 self-contained domain modules (attendance, site, employee, material, tool, metrics, report, auth, etc.)
- Consistent module pattern: Model → Service → Controller → Routes → Validation → Seed
- Auto-discovery `moduleLoader.js` dynamically registers all modules at `/api/v1/{name}` — no manual route wiring

### 10. Security Hardening
- Helmet for HTTP security headers (CSP, HSTS, XSS protection)
- XSS-Clean middleware for input sanitization
- Joi schema validation on all route inputs
- Redis-backed per-endpoint rate limiting
- bcrypt password hashing with account lockout protection

### 11. Internationalization
- Full English and Arabic support via i18next — frontend and backend
- Language detection from request headers
- Translatable error messages, notifications, and UI strings

### 12. Developer Experience & Ops
- Docker Compose setup: Express + MongoDB (Replica Set) + Redis + RabbitMQ for local development
- Swagger/OpenAPI auto-generated docs at `/api-docs` (116 documented endpoints)
- Winston structured logging with correlation IDs for request tracing
- Database seed scripts for demo data and initial setup
- GitHub Actions CI/CD: push to main → SSH into EC2 → pull → build → PM2 reload (zero-downtime)

---

## Scale & Scope

- **18 backend domain modules** covering the complete site operations lifecycle
- **42 frontend pages** (React, lazy-loaded, role-protected routes)
- **116 REST API endpoints** (fully Swagger documented)
- **4 user roles** with distinct access scopes and permissions
- **4 background workers** running independently via RabbitMQ
- **10+ third-party integrations**: AWS S3, AWS SES, Twilio, Firebase, Google OAuth, MongoDB, Redis, RabbitMQ
- **Offline-first PWA** — works without internet, syncs when connectivity returns

---

## Challenges Solved

**Challenge:** Field workers on construction sites frequently lose internet connectivity mid-shift, but attendance must still be recorded accurately.

**Solution:** Implemented an offline-first pattern using IndexedDB as a local queue. Workers enter attendance normally — the app stores records locally when offline. A `useOfflineSync` hook monitors connectivity and auto-syncs all queued records when internet is restored. UUID-based `offlineId` on every record ensures zero duplicates even if sync is triggered multiple times.

---

**Challenge:** Notifications (email, SMS, push) needed to be reliable without blocking the API response or degrading perceived performance.

**Solution:** Designed a RabbitMQ-based async worker system. The API controller publishes a message to the appropriate queue and immediately returns to the client. Dedicated workers (emailWorker, smsWorker, pushWorker) consume and process these jobs independently. A 3-retry DLQ strategy handles transient failures without data loss.

---

**Challenge:** Construction sites have different teams, materials, and tools, and management needed visibility across all sites simultaneously.

**Solution:** Every resource (attendance, material, tool, flat progress, photo) is scoped to a specific site. Role-based access ensures foremen only see their assigned sites. Admin dashboards aggregate across all sites via MongoDB aggregation pipelines, providing a unified management view.

---

**Challenge:** A painting workflow involves dozens of stages across multiple ceiling and wall types — the data model needed to capture granular per-flat stage completion without becoming unwieldy.

**Solution:** Designed a `FlatProgress` document with nested stage maps for each ceiling type (concrete/gypsum/wall). Each stage tracks completion status, timestamp, and who completed it. A virtual field auto-calculates overall completion percentage from the stage states, keeping queries simple while the model captures full detail.

---

## What I Would Do Differently

- Add a comprehensive test suite (Jest + Supertest) covering all 116 endpoints — currently relies on Swagger-based manual testing
- Move backend to TypeScript for stronger type safety across 18 modules
- Add distributed tracing (OpenTelemetry) for end-to-end observability in production
- Implement WebSocket or SSE for real-time dashboard updates (currently polling)

---

## Deployment Architecture

```
GitHub Push (main)
    │
    ▼
GitHub Actions
    ├── SSH into EC2
    ├── git pull
    ├── npm ci (backend)
    ├── npm run build (React/Vite)
    └── pm2 reload all
    │
    ▼
AWS EC2 Instance (Elastic IP)
    ├── Nginx
    │   ├── Serves React build (dist/) at /
    │   └── Proxies /api/* → Node.js :3000
    └── PM2 Cluster
        ├── API Server (Node.js)
        ├── emailWorker
        ├── smsWorker
        ├── pushWorker
        └── auditWorker
```

| Layer | Tool |
|-------|------|
| **Cloud Host** | AWS EC2 (Linux) with Elastic IP |
| **Frontend Serving** | Nginx — serves React build + reverse proxy |
| **Backend Process** | PM2 — clustering, auto-restart, log management |
| **CI/CD** | GitHub Actions — automated build and deploy on push to main |

---

## Repository

> Source code available privately. Can be shared or walked through during a technical interview upon request.

---

*Document prepared for portfolio / resume use. Last updated: March 2026.*
