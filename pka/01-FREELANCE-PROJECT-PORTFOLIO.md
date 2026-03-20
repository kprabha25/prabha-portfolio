# Freelance Project: Construction Site Painting Operations — Mobile App

> **Confidentiality Note:** Client name is withheld per standard freelance NDA. Full technical details, architecture decisions, and code samples available for discussion in interviews.

---

## Project Overview

Designed and built a **field operations management mobile app** for a painting and construction company as the sole mobile developer. The app provides foremen, field workers, and administrators real-time mobile access to manage day-to-day construction site painting operations.

The system replaces manual paper-based processes — covering attendance tracking, material inventory, tool management, flat-by-flat progress tracking, photo documentation, and operational reporting — all from a single cross-platform mobile app.

---

## My Role

**Freelance Mobile Developer** (sole mobile engineer)

- Architected the entire React Native + Expo application from scratch
- Designed the Redux state management structure across 15 domain slices
- Built an offline-first attendance system for poor connectivity environments
- Integrated JWT authentication with automatic token refresh
- Implemented RBAC-controlled navigation and UI for multiple user roles
- Set up EAS Build pipeline for Android and iOS production deployments
- Configured i18n for English and Arabic (RTL) support

---

## Tech Stack

| Category | Technologies |
|---|---|
| **Framework** | React Native 0.81.5, Expo SDK 54, JavaScript |
| **State Management** | Redux Toolkit 2.x, React-Redux 9.x |
| **Navigation** | React Navigation 7.x (native-stack, drawer, bottom-tabs) |
| **HTTP Client** | Axios 1.x (with JWT interceptors + auto-refresh) |
| **Authentication** | JWT (access + refresh tokens), Google OAuth 2.0 |
| **Secure Storage** | expo-secure-store (OS-level encrypted token storage) |
| **Push Notifications** | expo-notifications + Firebase Cloud Messaging (FCM) |
| **Location** | expo-location (GPS capture for login + attendance) |
| **Camera / Photos** | expo-image-picker (camera + gallery + compression) |
| **Offline Support** | @react-native-community/netinfo + AsyncStorage queue |
| **Charts** | react-native-chart-kit (bar, pie, line charts) |
| **UI Libraries** | Ant Design React Native, React Native Paper |
| **i18n** | i18next + react-i18next (English + Arabic / RTL) |
| **Date Handling** | dayjs |
| **Build / Deploy** | EAS Build, EAS Update (OTA) |

---

## Key Technical Contributions

### 1. Offline-First Attendance System
- Built a local queue (AsyncStorage + Redux) that stores attendance records when the device is offline
- Auto-syncs queued entries when connectivity is restored via `netinfo` listener
- Server-side `offlineId` deduplication prevents duplicate submissions on re-sync
- Queue capped at 100 entries with 7-day TTL to prevent memory growth on long-offline devices

### 2. JWT Auth with Auto Token Refresh
- Implemented full access + refresh token lifecycle in Axios interceptors
- Single global `isRefreshing` flag prevents multiple simultaneous refresh calls
- Failed requests during token refresh are queued and automatically replayed after new token is issued
- Tokens stored in `expo-secure-store` (OS-level encryption, not plain AsyncStorage)

### 3. Role-Based Access Control (RBAC)
- 40+ granular permissions mapped to 4 default roles: Superadmin, Admin, Foreman, Labour
- RBAC enforced at three levels: navigation drawer (hidden items), screen render (conditional buttons), and guard components (protected sections)
- Field workers only see their personal attendance; foremen manage their whole site; admins have full access

### 4. Multi-Domain Feature Modules
- 15 API modules and 15 Redux slices covering the full operational domain:
  sites, employees, attendance, materials, tools, photos, flat progress, manpower, reports, metrics, admin users, roles, activity log
- Domain-driven structure: each feature owns its API module, Redux slice, and screen stack

### 5. Flat-by-Flat Progress Tracking
- Visual grid interface showing completion status for every flat in every building on the site
- Real-time status update (pending → in-progress → completed) with assignee and timestamp
- Completion percentage surfaced on the dashboard for at-a-glance progress visibility

### 6. Photo Documentation System
- In-app camera and gallery picker with automatic image compression (max 1024×1024, quality 0.7)
- Photos tagged with site, category, timestamp, and uploader before multipart upload
- Extended 60-second timeout on upload requests to handle large images on slow connections

### 7. Arabic RTL Internationalization
- Full English and Arabic translation coverage via i18next
- Device locale auto-detected on first launch with user-switchable preference from Profile screen
- RTL layout direction automatically applies when Arabic is selected — no separate codebase

### 8. Push Notifications (FCM)
- FCM token registered with the backend on every login to ensure device targeting stays current
- Foreground notifications displayed as in-app toasts; background notifications use the system tray
- Notification types: attendance confirmations, low stock alerts, site assignments, shift reminders

### 9. EAS Build & OTA Updates
- Configured EAS Build profiles for internal APK previews and Play Store / App Store submissions
- EAS Update enables over-the-air JavaScript bundle updates for hotfixes that bypass store review

### 10. Charts & Reporting
- Attendance, productivity, and manpower reports with interactive bar, pie, and line charts via react-native-chart-kit
- All reports filtered by site, date range, and employee — driven by the same API layer

---

## Scale & Scope

- **36+ screens** across 12 operational domains
- **15 API modules** covering all backend endpoints
- **15 Redux slices** for independent domain state
- **4 user roles** with distinct permissions and navigation experiences
- **2 languages** (English + Arabic/RTL) with auto-detection
- **1 app** deployed to both Android and iOS from a single codebase

---

## Challenges Solved

**Challenge:** Construction sites have unreliable or no internet connectivity. Attendance — the most critical daily operation — could not fail when workers are offline.
**Solution:** Built a persistent offline queue (AsyncStorage + Redux) with automatic sync on reconnection. The `offlineId` field ensures the server can idempotently handle re-submitted records without duplicates, even if the sync is triggered multiple times before the server confirms success.

---

**Challenge:** Multiple user roles (Superadmin, Admin, Foreman, Labour) need dramatically different app experiences without maintaining separate apps or codebases.
**Solution:** Implemented permission-driven rendering at every layer — navigation items, action buttons, and guard components all read from a central permissions utility. Adding a new role or adjusting permissions requires only a config change, not UI restructuring.

---

**Challenge:** GPS capture for attendance needed to be mandatory but could not block the app if location services were slow or unavailable.
**Solution:** Applied a 15-second timeout to all location requests. For login, GPS capture is non-blocking (fires in the background). For attendance, it is blocking but gracefully degrades with a timeout error — the user is informed and can retry rather than the app hanging indefinitely.

---

**Challenge:** Supporting both English and Arabic within the same React Native app without layout hacks.
**Solution:** Integrated i18next with expo-localization for auto-detection and React Native's built-in RTL support (`I18nManager.forceRTL`). Switching to Arabic flips layout direction app-wide at runtime without a restart.

---

## What I Would Do Differently / Improvements Noted

- Add a test suite (Jest + React Native Testing Library) — the app currently relies on manual QA through Expo Go
- Migrate to TypeScript for stronger type safety across 15+ Redux slices and 15 API modules
- Implement a Sentry integration for production crash reporting and performance monitoring
- Add a CI/CD pipeline (GitHub Actions + EAS) to automate builds on merge to main

---

## Repository

> Source code available privately. Can be shared or walked through during a technical interview upon request.

---

*Document prepared for portfolio / resume use. Last updated: March 2026.*
