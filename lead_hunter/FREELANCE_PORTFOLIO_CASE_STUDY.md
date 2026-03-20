# Freelance Project: AI-Powered Lead Generation Platform

**Role:** Freelance Full-Stack Developer
**Project Type:** Client Web Application (Freelance Engagement)
**Duration:** 2024 – 2025
**Status:** Delivered & Production-Ready

---

## Project Overview

Designed and built a full-stack, AI-powered lead generation platform for a B2B client operating in the UAE market. The client needed an automated way to discover, enrich, and manage business leads at scale — replacing a slow, manual research process.

The platform lets users describe what they need in plain language (e.g., *"Find me 200 dentists in Dubai"*) and an AI agent autonomously searches, deduplicates, enriches, and stores the results — all in real time.

---

## Problem Statement

The client's sales team was spending hours manually searching Google Maps, copying business details into spreadsheets, and constantly hitting duplicates. They needed:

- A scalable way to generate targeted lead lists on demand
- Deduplication across past searches to avoid wasted API spend
- Real-time feedback on what the AI is doing during a search
- A secure, multi-user platform with role-based access control
- Support for Arabic and English-speaking team members

---

## My Role & Responsibilities

As the sole developer on this engagement, I was responsible for the full lifecycle:

- **Architecture design** — chose the right tech stack and designed a modular, microservice-ready architecture
- **Backend development** — built the entire REST API, authentication system, AI agent loop, and async worker pipeline
- **Frontend development** — built the React dashboard including real-time progress streaming
- **Infrastructure** — containerized the full stack with Docker; configured PM2 for production deployment
- **Security** — implemented enterprise-grade security hardening across all layers
- **Delivery** — wrote full technical documentation and handed off a production-ready system

---

## Technical Architecture

### High-Level Design

```
React Frontend (Vite + Ant Design)
        ↓  REST API + SSE (real-time)
Express.js Backend (Node.js)
        ↓
┌───────────────────────────────────┐
│  MongoDB  │  Redis  │  RabbitMQ  │
└───────────────────────────────────┘
        ↓ (async workers)
AWS SES (Email) │ Twilio (SMS) │ Firebase (Push)
```

### Stack

| Layer | Technology |
|---|---|
| Frontend | React 18, Vite, Ant Design, React Router v7, i18next |
| Backend | Node.js 18, Express.js |
| AI | Anthropic Claude API (tool use / agentic loop) |
| Database | MongoDB 7 (replica set) |
| Cache | Redis 7 |
| Queue | RabbitMQ 3 |
| Auth | Passport.js (JWT, Google OAuth) |
| Cloud | AWS SES (email), AWS S3 (files) |
| SMS / Push | Twilio, Firebase Admin SDK |
| DevOps | Docker, Docker Compose, PM2 |
| Security | Helmet.js, bcrypt, rate-limit-redis, xss-clean, express-mongo-sanitize |

---

## Key Features Built

### 1. AI Agent for Lead Discovery
- Integrated **Claude AI with tool use** to run an autonomous agentic loop
- The agent decides how to break down a query (e.g., by district), calls search tools, enriches missing contact data, and saves results — all without user intervention
- Implemented **cancellation support** via Redis flag so users can stop a search mid-run
- Streamed agent progress to the frontend in real time using **Server-Sent Events (SSE)** — users see the agent "thinking" and each tool call as it happens
- Tracked token usage and cost estimates per search session

### 2. Enterprise Authentication System
- JWT-based auth with **access tokens (15 min) and refresh tokens (7 days, single-use rotation)**
- Token blacklisting in Redis for secure logout
- **Google OAuth** SSO, email/password with OTP verification, phone OTP (Twilio)
- Account lockout after 5 failed login attempts (30-minute lock)
- OTP rate limiting (max 3 requests per 15 minutes)
- Full audit log of all authentication events

### 3. Role-Based Access Control (RBAC)
- Dynamic permission system using `resource:action` format (e.g., `users:read`, `leads:write`)
- Predefined roles (superadmin, admin, user) with granular override capability
- Route-level and UI-level permission guards

### 4. Async Notification Pipeline
- Email, SMS, and push notifications processed asynchronously via **RabbitMQ workers**
- Separate Node.js worker processes (emailWorker, smsWorker, pushWorker, auditWorker)
- In-app notifications with read/unread state persisted to MongoDB

### 5. Data Deduplication & Search History
- Leads deduplicated by **Google Places `place_id`** — silently skips already-saved businesses
- **Search history tracking** prevents re-searching the same area/category, saving API costs
- Per-session stats: how many leads saved vs skipped

### 6. Pluggable Data Source Layer
- Abstracted data retrieval behind a uniform interface — supports **Google Maps API** and **Outscraper** interchangeably via environment config
- Easy to add new providers without touching agent logic

### 7. Multilingual UI (English + Arabic)
- Full i18n on both frontend and backend using **i18next**
- Language detected from browser settings or user preference stored in localStorage

### 8. File Management & Export
- AWS S3 integration with signed URLs for secure, time-limited access
- Lead export to Excel/CSV for offline use

---

## Security Highlights

Security was a primary concern given the platform handles business data and user credentials:

- **NoSQL injection prevention** — express-mongo-sanitize strips `$` operators from all inputs
- **XSS protection** — xss-clean sanitizes body and query params
- **Password hashing** — bcrypt with 12 salt rounds
- **Redis-backed rate limiting** — separate limits for auth endpoints vs general API
- **Security headers** — Helmet.js configures CSP, HSTS, X-Frame-Options, and more
- **CORS** — explicit origin whitelist, no wildcard
- **Request tracing** — Correlation IDs propagated across all services for debugging

---

## Architecture Decisions & Trade-offs

**Monorepo with module auto-discovery:** I chose to auto-load route modules so that adding new features requires only creating a new module directory — no changes to the main app file. This made the project easy to extend without risk of breaking existing functionality.

**RabbitMQ for async tasks instead of in-process:** Decoupling email/SMS/push into separate worker processes means a spike in notifications never blocks API request handling, and workers can be scaled independently.

**SSE over WebSockets for real-time streaming:** The agent communication is strictly server-to-client (one-way), so SSE was simpler to implement, requires no additional infrastructure, and works reliably through proxies.

**Redis for token management:** Storing refresh token hashes and blacklisted JTIs in Redis gives millisecond-level revocation with no database round-trip on every authenticated request.

---

## Outcomes

- Delivered a production-ready platform meeting all client requirements
- Reduced manual lead research time significantly through full automation
- Scalable architecture ready for multi-region deployment or microservice extraction
- Full API documentation (Swagger/OpenAPI) handed off with the codebase
- Comprehensive technical guides covering setup, queue/cache system, and seeding

---

## What I Would Do Differently

- Add end-to-end tests (Cypress/Playwright) for critical user flows from the start
- Introduce an API gateway layer earlier for better rate-limit centralization at scale
- Build a more granular cost dashboard showing per-user AI token consumption

---

## Repository Structure

```
git-lead-hunter/
├── backend/
│   ├── src/
│   │   ├── modules/       # Feature modules (agent, auth, leads, users, ...)
│   │   ├── config/        # Environment config with Joi validation
│   │   ├── middleware/    # Auth, rate-limit, sanitization, i18n
│   │   ├── workers/       # RabbitMQ consumers (email, SMS, push, audit)
│   │   └── utils/         # Shared helpers
│   ├── docker-compose.yml
│   └── ecosystem.config.js  # PM2 production config
├── frontend/
│   └── src/
│       ├── components/    # Reusable UI components
│       ├── pages/         # Route-level pages
│       ├── contexts/      # Auth & leads context providers
│       └── locales/       # i18n translation files
└── docs/                  # Technical guides (this file lives here)
```

---

## Contact

Available for freelance engagements. Feel free to reach out via GitHub or LinkedIn.
