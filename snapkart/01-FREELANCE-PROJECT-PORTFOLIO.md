# Freelance Project: E-Commerce Marketplace Platform — Backend API

> **Confidentiality Note:** Client name is withheld per standard freelance NDA. Full technical details, architecture decisions, and code samples available for discussion in interviews.

---

## Project Overview

Designed and built the complete backend for a **full-featured e-commerce marketplace** as the sole backend developer. The platform provides a comprehensive product catalog, multi-vendor store support, user authentication, blog/content management, and an admin panel for managing all marketplace operations.

The entire backend was architected and developed from the ground up — covering authentication with token rotation, product variant management, image optimization, content management, and a full admin analytics dashboard.

---

## My Role

**Freelance Backend Developer** (sole backend engineer)

- Architected the entire backend system from scratch using TypeScript
- Designed the MongoDB schema with 12 collections and optimized indexes
- Built a layered architecture: Routes → Controllers → Services → Models
- Implemented JWT authentication with dual-secret admin security
- Integrated image upload and optimization pipeline (Multer + Sharp)
- Wrote comprehensive API documentation and setup guides

---

## Tech Stack

| Category | Technologies |
|---|---|
| **Runtime / Framework** | Node.js, Express.js 4.x, TypeScript 5.x |
| **Database** | MongoDB (Mongoose ODM 8.x) |
| **Authentication** | JWT (access + refresh tokens), bcryptjs |
| **File Upload** | Multer (disk storage), Sharp (image optimization) |
| **Validation** | Joi (schema-based request validation) |
| **Security** | Helmet, CORS, express-rate-limit, express-mongo-sanitize |
| **Utilities** | slugify, compression, cookie-parser, morgan |
| **Testing** | Jest |
| **Dev Tools** | nodemon, ESLint, Prettier |

---

## Key Technical Contributions

### 1. JWT Authentication with Dual-Secret Security
- Stateless auth with short-lived access tokens (15 min) and refresh tokens (7 days)
- Refresh tokens stored in HTTP-only cookies and in the user's document for server-side invalidation
- Separate JWT secret for admin routes (`ADMIN_JWT_SECRET`) — admin tokens cannot be used on regular user endpoints and vice versa
- Token rotation on refresh; logout invalidates the specific refresh token from the user's stored array

### 2. Layered Service Architecture
- Clean separation: Routes handle middleware chaining, Controllers handle request/response, Services contain all business logic and DB queries, Models define schemas and indexes
- All controllers wrapped in `asyncHandler` — eliminates try/catch boilerplate and centralizes error handling
- Consistent API response envelope (`{ success, data, pagination, error }`) across all endpoints

### 3. Product Catalog with Variants & Sizes
- Products support multiple variants (e.g., color options) — each with its own price, sale price, images, and size list
- Flexible size model: supports named sizes ("S", "M", "XL") with an optional short character ("S", "M", "L")
- Rich metadata: tags, specifications (key-value pairs), multiple description points
- Multiple relationship types: categories (array), brand (single), collections (array), stores (array)
- Compound MongoDB indexes for performant filtered queries (category + brand + price + flags)

### 4. Slug Auto-Generation System
- Mongoose pre-save hooks automatically generate SEO-friendly slugs from name/title fields using `slugify`
- Applied to all 10 content types: products, categories, brands, collections, stores, banners, promotions, posts, post categories, pages
- Enables clean URLs: `/api/products/slug/mens-slim-fit-jeans-blue`

### 5. Image Upload & Optimization Pipeline
- Multer configured for disk storage with organized subdirectories (`/uploads/products/`, `/uploads/thumbnails/`)
- MIME type validation (JPEG, PNG, WebP, GIF) and 5MB size limit enforced at middleware level
- Unique filenames generated with timestamp + random suffix to prevent collisions
- Sharp integration for server-side image optimization before storage
- Uploaded files served as static assets via Express at `/api/uploads/:filename`

### 6. Content Management System (Blog + Pages)
- Full blog system: posts, post categories, related posts
- Rich content field supporting HTML/markdown
- Static pages system with 5 predefined types (FAQ, terms, privacy, shipping, payment methods)
- Each page type supports both a content field (HTML) and a structured FAQs array

### 7. Security Hardening
- Helmet for comprehensive HTTP security headers (CSP, X-Frame-Options, HSTS, etc.)
- Two-tier rate limiting: global (100 req/15 min per IP) and strict auth rate limiting (5 req/15 min)
- express-mongo-sanitize strips `$` operators from inputs to prevent NoSQL injection
- Whitelist-based CORS with credential support
- bcrypt with 10 salt rounds for password hashing

### 8. Admin Dashboard & Analytics
- Dashboard stats endpoint aggregating counts across all collections in parallel
- Product analytics: breakdown by category, brand, and status flags (featured, on-sale, out-of-stock)
- User analytics and management with status toggle
- Full CRUD for all 10 content types protected behind admin middleware

### 9. Request Validation Layer
- Joi schemas for all write operations (create/update)
- Validation middleware wrappers: `validateBody()` and `validateQuery()` for consistent DX
- Validation errors return structured field-level messages for clean frontend error display

### 10. Developer Experience
- TypeScript throughout — interfaces for all models, request types, and service params
- Comprehensive npm scripts: dev, build, test, lint, seed, seed:refresh, debug utilities
- Database seed script with 19 sample products, 15 categories, 18 brands, 10 collections, 4 stores, 6 blog posts
- Multiple setup guides: QUICK_START.md, SETUP_GUIDE.md, API_DOCUMENTATION.md, UPLOAD_TROUBLESHOOTING.md

---

## Scale & Scope

- **12 MongoDB collections** covering the full e-commerce domain
- **~50+ API endpoints** split across public, private, and admin access levels
- **2 user roles** (user, admin) with separate JWT secrets and middleware guards
- **10 content types** all with auto-slug, CRUD, and SEO-friendly URL access
- Includes a React admin panel (`admin-panel/`) for full marketplace management

---

## Challenges Solved

**Challenge:** Admin operations needed stronger isolation than just a role check — a compromised user token should not be usable against admin endpoints even if the user's role were somehow elevated.

**Solution:** Implemented a dual-secret JWT system where admin tokens are signed with a completely separate `ADMIN_JWT_SECRET`. The `requireAdmin` middleware uses a different verification secret than `requireAuth`, so tokens from one strategy are cryptographically invalid against the other.

---

**Challenge:** Product images needed to be organized, uniquely named, and optimized without blocking the request cycle.

**Solution:** Built a Multer pipeline with disk storage writing to typed subdirectories (`/products/`, `/thumbnails/`), generating collision-safe filenames. Sharp processes images after storage for optimization. The upload endpoint returns the file URL immediately for use in subsequent product create/update calls.

---

**Challenge:** Supporting rich product filtering (category, brand, price range, flags, search) without N+1 query issues or slow scans.

**Solution:** Designed compound MongoDB indexes covering the most common filter combinations (e.g., `isActive + isFeatured`, `productCategories + brand + price`). All list queries are built dynamically from validated query params and run as a single Mongoose query with pagination.

---

**Challenge:** Static pages (FAQ, terms, privacy) needed to support both structured FAQ data and free-form HTML content, with each page type being unique and manageable via the admin panel.

**Solution:** Modeled pages with a unique `type` enum field, a free-form `content` string, and a structured `faqs` array of `{ question, answer }` objects — allowing each page to use whichever format suits its content type, all managed through a single admin interface.

---

## What I Would Do Differently / Improvements Noted

- Add a comprehensive test suite (Jest + Supertest) — current setup has the Jest config but coverage is minimal
- Migrate file storage to AWS S3 or similar cloud storage for production scalability
- Add full-text search support via MongoDB Atlas Search or Elasticsearch for better product search
- Implement a formal CI/CD pipeline (GitHub Actions) with lint, test, and build checks
- Add OpenAPI/Swagger documentation generation from route definitions
- Consider adding Redis for refresh token blacklisting as the user base scales

---

## Repository

> Source code available privately. Can be shared or walked through during a technical interview upon request.

---

*Document prepared for portfolio / resume use. Last updated: March 2026.*
