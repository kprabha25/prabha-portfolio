# Freelance Project: Multi-Vendor Food Delivery Platform — Backend

> **Confidentiality Note:** Client name is withheld per standard freelance NDA. Full technical details, architecture decisions, and code samples available for discussion in interviews.

---

## Project Overview

Designed and built the complete backend for a **multi-vendor food delivery marketplace** (similar to UberEats / Talabat) as the sole backend developer. The platform connects **customers**, **restaurant vendors**, and **delivery partners** through three dedicated mobile apps and a web-based admin panel.

The entire backend was architected and developed from the ground up — covering authentication, real-time tracking, async notifications, multi-payment processing, and full marketplace business logic.

---

## My Role

**Freelance Backend Developer** (sole backend engineer)

- Architected the entire backend system from scratch
- Designed the database schema and API structure
- Integrated all third-party services (payments, notifications, file storage)
- Built real-time order tracking with Socket.IO
- Set up Docker-based development and deployment environment
- Wrote Swagger/OpenAPI documentation for all endpoints

---

## Tech Stack

| Category | Technologies |
|---|---|
| **Runtime / Framework** | Node.js, Express.js v5, JavaScript |
| **Database** | MongoDB (Mongoose ODM), Redis |
| **Message Queue** | RabbitMQ |
| **Real-time** | Socket.IO v4 |
| **Authentication** | JWT (access + refresh tokens), Passport.js, Google OAuth 2.0 |
| **Payments** | Stripe, Razorpay |
| **Cloud / Storage** | AWS S3 (file uploads), AWS SES (email) |
| **Notifications** | Firebase Cloud Messaging (FCM), Twilio (SMS/OTP) |
| **Security** | Helmet, bcryptjs, express-rate-limit, CORS |
| **DevOps** | Docker, Docker Compose |
| **API Docs** | Swagger / OpenAPI |
| **Logging** | Winston, Morgan |
| **i18n** | i18next (English + Arabic) |

---

## Key Technical Contributions

### 1. Multi-Role Authentication System
- Built JWT-based auth with short-lived access tokens (15 min) and refresh tokens (7 days)
- Redis token blacklist for secure logout and session invalidation
- 4 separate auth strategies: Customer, Restaurant, Delivery Partner, Admin
- Google OAuth 2.0 single sign-on integration
- Phone-based OTP registration via Twilio SMS

### 2. Real-Time Order Tracking
- Socket.IO implementation for live GPS location updates from delivery partners
- Event-driven broadcasts to customers and restaurants on order status changes
- Order-specific communication channels with room-based isolation

### 3. Async Notification Pipeline (RabbitMQ)
- Designed a message queue architecture with 4 dedicated workers:
  - **Email Worker** — AWS SES transactional emails
  - **SMS Worker** — Twilio OTP and alerts
  - **Push Worker** — Firebase FCM mobile push notifications
  - **Audit Worker** — Action logging for compliance

### 4. Multi-Payment Integration
- Stripe for international card payments
- Razorpay for UPI / Indian payment methods
- Internal wallet system with credit balance and transaction history
- COD (cash on delivery) support

### 5. Modular Domain-Driven Architecture
- 25 feature modules (orders, items, restaurants, delivery, payments, promos, etc.)
- Consistent module pattern: Model → Controller → Routes → Service
- Dynamic module loader for zero-friction feature additions

### 6. File Upload with Dual-Mode Storage
- Multer + Multer-S3 pipeline: local filesystem in dev, AWS S3 in production
- File type and size validation middleware
- Handles profile images, restaurant banners, certificates, menu item photos

### 7. Standardized API Response Layer
- Centralized response handler ensuring consistent JSON envelope across all endpoints
- Snake_case / camelCase conversion middleware for clean client contracts
- Standardized HTTP status codes and error formats across all 25+ modules

### 8. Security Hardening
- Helmet for HTTP security headers (CSP, HSTS, etc.)
- Per-endpoint rate limiting to prevent abuse
- bcrypt password hashing
- Whitelist-based CORS configuration
- express-validator input sanitization on all routes

### 9. Internationalization
- Full Arabic and English support via i18next
- Language detection from request headers
- Translatable error messages and notifications

### 10. Developer Experience & Ops
- Docker Compose setup (Express + MongoDB + Redis + RabbitMQ)
- Swagger/OpenAPI auto-generated docs at `/api-docs`
- Winston structured logging with request correlation IDs
- Database seed scripts for local development

---

## Scale & Scope

- **~25 feature modules** covering the full marketplace lifecycle
- **4 user roles** with distinct authorization and app experiences
- **3 mobile apps** + 1 admin web panel served from this single API
- **10+ third-party service integrations**
- Full order lifecycle: discovery → cart → checkout → payment → real-time tracking → delivery → review

---

## Challenges Solved

**Challenge:** Notifications needed to be reliable but could not block the request-response cycle.
**Solution:** Designed a RabbitMQ-based async worker system — the API enqueues notification jobs and returns immediately; dedicated workers process them independently, ensuring notification failures never affect the user-facing response.

---

**Challenge:** Supporting multiple payment gateways (Stripe, Razorpay, wallet, COD) without duplicating checkout logic.
**Solution:** Abstracted payment logic behind a strategy pattern — the checkout endpoint accepts a `paymentMode` parameter and delegates to the appropriate gateway handler, keeping the core flow clean and extensible.

---

**Challenge:** Managing 3 distinct user roles (customer, restaurant, delivery partner) each with different permissions, dashboards, and data visibility.
**Solution:** Built 4 independent auth middleware strategies and RBAC-controlled route guards, so each app type only accesses its own data scope — enforced at the API level, not just the client.

---

## What I Would Do Differently / Improvements Noted

- Add a comprehensive test suite (Jest + Supertest) — currently relies on manual testing via Swagger
- Move to TypeScript for stronger type safety across 25+ modules
- Add distributed tracing (e.g., OpenTelemetry) for deeper observability in production
- Implement a formal CI/CD pipeline (GitHub Actions)

---

## Repository

> Source code available privately. Can be shared or walked through during a technical interview upon request.

---

*Document prepared for portfolio / resume use. Last updated: March 2026.*
