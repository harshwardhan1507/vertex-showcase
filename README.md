<div align="center">

<br />

<img src="https://img.shields.io/badge/Next.js-16.2.1-000000?style=for-the-badge&logo=nextdotjs&logoColor=white" />
<img src="https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
<img src="https://img.shields.io/badge/Supabase-Postgres-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white" />
<img src="https://img.shields.io/badge/Deployed-Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white" />
<img src="https://img.shields.io/badge/Status-Live-22c55e?style=for-the-badge" />
<img src="https://img.shields.io/badge/Progress-87%25-6366f1?style=for-the-badge" />

<br /><br />

# Vertex — Campus Operating System

### *Where campus life connects.*

**[🔗 Live Demo](https://vertexcampusos.vercel.app)** &nbsp;·&nbsp; **[📬 Request Code Access](mailto:harshwardhansingh1507@gmail.com)** &nbsp;·&nbsp; **[🌐 Portfolio](https://harshwardhanportfolio.vercel.app)**

<br />

</div>

---

## What Is Vertex?

Vertex is a **full-stack campus event and club management platform** built for Indian college students, club leads, and administration. It eliminates the chaos of WhatsApp groups, manual certificates, and paper attendance by bringing everything into one unified system.

This is not a tutorial project. Vertex is a **production-grade platform** with:

- **5 distinct user roles** — Student, Committee, Club Lead, Professor, Dean
- **10 functional modules** — from QR attendance to public document verification
- **Transactional database safety** — PostgreSQL stored functions with row-level locking
- **Real-time push notifications** via Firebase Cloud Messaging
- **Automatic PDF generation** for certificates and OD letters
- **One codebase → Web + Android + iOS** via Capacitor

> Built solo. Deployed to production. Designed to scale.

---

## What It Does

<details>
<summary><strong>👨‍🎓 For Students</strong></summary>

- Discover and register for campus events
- Receive push notifications 1 day and 1 hour before events
- Get certificates automatically after attending
- Build a **VScore** — a real-time campus participation grade and percentile ranking
- View and download OD letters, share certificates directly to LinkedIn

</details>

<details>
<summary><strong>🎯 For Committee Members</strong></summary>

- Track upcoming event timelines and assigned tasks
- Scan student QR tickets for live attendance marking (RollCall)
- Post volunteer requirements and manage applications

</details>

<details>
<summary><strong>🏆 For Club Leads</strong></summary>

- Create events and submit for professor approval
- Generate and email certificates + OD letters in one click
- View registration analytics and club follower growth
- Search students by VScore grade for targeted inductions
- Direct message event registrants and followers
- Manage committee roster and club profile

</details>

<details>
<summary><strong>🎓 For Professors (In-Charge)</strong></summary>

- Review and approve or reject event proposals from assigned clubs
- Export full club attendance and student VScore reports
- Supervise club activities and operations

</details>

<details>
<summary><strong>🏛️ For the Dean / HOD</strong></summary>

- Platform-wide directory — students, clubs, staff
- Suspend/deactivate user or club accounts
- Set college-wide blackout dates and pin featured events
- Approve new club creation requests
- Export semester summary and department-wise reports
- Manage global OD letter template and homepage banner

</details>

---

## Feature Modules

| Module | Description |
|--------|-------------|
| **EventPass** | Event discovery, registration, QR ticket generation |
| **PulseAlert** | Firebase push notifications — 24h and 1h reminders |
| **ClubHub** | Club profile pages, follow system, announcements, analytics |
| **RollCall** | Live QR-based attendance scanning by committee members |
| **CertifyMe** | Auto certificate generation, PDF storage, email delivery |
| **ODPass** | OD letter generation, auto-sent to student + professor |
| **TrustMark** | Public document verification via unique UUID + embedded QR |
| **VScore** | Real-time participation scoring, grade, percentile, leaderboard |
| **InsightBoard** | Analytics dashboards for Club Leads and Dean (Recharts) |
| **GateKeeper** | Dean & Professor controls — approvals, governance, announcements |

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| Framework | Next.js 16.2.1 (App Router) |
| Language | TypeScript 5 |
| Styling | Tailwind CSS v4 |
| Database | Supabase (Postgres + RLS + Edge Functions) |
| Auth | Supabase Auth (SSR, Google OAuth) |
| Storage | Supabase Storage |
| Notifications | Firebase Cloud Messaging (FCM) + Firebase Admin |
| PDF Generation | pdf-lib |
| Email | Resend |
| QR Code | qrcode, html5-qrcode |
| State Management | Zustand |
| Forms & Validation | React Hook Form + Zod |
| Analytics & Charts | Recharts |
| Mobile | Capacitor (iOS & Android wrapper) |
| PWA | next-pwa |
| Deployment | Vercel |

---

## Platform Strategy

Vertex ships from **one Next.js codebase** to two platforms — a web app on Vercel and a native Android/iOS app via Capacitor. Capacitor loads the live Vercel URL as a hosted web view, meaning all Next.js features (API routes, SSR, server components) work perfectly in the native shell. No code duplication. No separate repos.

```
               ┌──> Vercel (Web)
               │
Next.js Code ──┤
               │             ┌──> Android (Google Play)
               └──> Capacitor ──┤
                             └──> iOS (TestFlight)
```

| Platform | Auth | API Routes | Push Notifications | Camera (QR) |
|----------|------|------------|-------------------|-------------|
| **Web** | Supabase SSR | Next.js API routes | Firebase FCM | Web API |
| **App** | Supabase SSR via web view | Next.js API routes via web view | Capacitor Push plugin | Capacitor Camera plugin |

> **Why not a static export?**
> Next.js SSR and API routes are core to how Vertex works — dynamic role routing, cookie-based sessions, and server-side data fetching all require a live server. A hosted web view keeps the full power of Next.js while wrapping it in native mobile shells.

---

## Architecture

### Role-Based Access Control (RBAC)

Every request passes through `middleware.ts` — a Next.js 16 middleware layer that intercepts each request, validates the Supabase session from cookies, and enforces role-based routing before any page loads.

```
User Request
      ↓
middleware.ts  (runs on every matched route — Next.js 16 proxy)
      ↓
Supabase session check from cookies
      ↓
No session          → redirect to /login
Valid session       → role check
      ↓
role = 'student'    → /dashboard, /events, /profile
role = 'committee'  → /committee/dashboard, /committee/scan
role = 'club_lead'  → /club-lead/dashboard, /club-lead/events
role = 'professor'  → /professor/dashboard
role = 'dean'       → /dean/dashboard
      ↓
Wrong role on wrong route → redirect to correct dashboard
```

### Three-Client Database Layer

```
supabase.ts          (browser client)
  └── Used in Client Components
  └── Respects RLS — users can only query their own data

supabase-server.ts   (server client)
  └── Used in Server Components and API routes
  └── Reads session from cookies, respects RLS

supabase-admin.ts    (admin client)
  └── Used only in trusted server-side API routes
  └── Bypasses RLS — for certificate generation, OD delivery
  └── Never exposed to the browser
```

### Transactional Safety

Registration and attendance use **PostgreSQL stored functions with row-level locking** — preventing race conditions during simultaneous high-volume registrations. Capacity enforcement, waitlist management, and check-in window validation are enforced at the database level, not the application layer.

```sql
-- Registration enforced via stored function
register_for_event_transactional(p_event_id, p_user_id)
  → Acquires row lock on event
  → Checks capacity + waitlist + duplicate registration
  → Inserts or waitlists atomically

-- Attendance enforced via stored function
mark_attendance_transactional(p_registration_id, p_scanner_id)
  → Validates check-in window
  → Verifies scanner role
  → Marks attended + recalculates VScore
```

---

## Request Flows

<details>
<summary><strong>Authentication Flow</strong></summary>

```
User opens app
      ↓
middleware.ts runs on every matched request
      ↓
Supabase checks session cookie
      ↓
No session     → redirect to /login
Valid session  → role-based redirect to correct dashboard
      ↓
Login → email + password or Google OAuth
      ↓
Supabase Auth validates → session cookie set
      ↓
Redirect to role dashboard
```

</details>

<details>
<summary><strong>Event Registration Flow</strong></summary>

```
Student clicks Register
      ↓
POST /api/register  (event_id + user_id)
      ↓
Checks: authenticated? event open? capacity available? already registered?
      ↓
register_for_event_transactional() called with row-level lock
      ↓
Row inserted into registrations table
      ↓
QR ticket generated with registration_id
      ↓
Ticket shown in dashboard + emailed via Resend
```

</details>

<details>
<summary><strong>Attendance + Document Generation Flow</strong></summary>

```
Committee opens event → Scan Attendance (RollCall)
      ↓
Student shows QR ticket → committee scans
      ↓
POST /api/attendance  (registration_id)
      ↓
Role auth check + check-in window enforcement
      ↓
registrations → attended = true
VScore recalculates automatically
      ↓
Club Lead clicks "Generate Certificates + OD Letters"
      ↓
For each attended student:
  ├── pdf-lib generates certificate → Supabase Storage
  ├── pdf-lib generates OD letter  → Supabase Storage
  ├── Unique UUID + QR code embedded in each document
  ├── Resend emails certificate to student
  └── Resend emails OD letter to student + professor
      ↓
Documents appear in student dashboard instantly
```

</details>

<details>
<summary><strong>Document Verification Flow (TrustMark)</strong></summary>

```
Professor or recruiter receives certificate / OD letter
      ↓
Scans embedded QR code
      ↓
Browser opens vertexcampusos.vercel.app/verify/[uuid]
      ↓
GET /api/verify/[id] → queries certificates table by UUID
      ↓
Returns: student name, event, date, club, attended status
      ↓
Page shows VERIFIED ✅  or  INVALID ❌
No login required — fully public endpoint
```

</details>

<details>
<summary><strong>Push Notification Flow (PulseAlert)</strong></summary>

```
Supabase Edge Function runs on cron schedule (every hour)
      ↓
Queries events starting in next 24 hours
Queries events starting in next 1 hour
      ↓
For each match → fetch FCM tokens of all registered students
      ↓
Firebase Cloud Messaging delivers push to device or browser
      ↓
Notification stored in notifications table (read / unread state)
```

</details>

---

## Project Structure

```
vertex/
├── app/
│   ├── (auth)/         # Login, signup, OAuth callback
│   ├── (student)/      # Student shell: dashboard, events, clubs, certs, etc.
│   ├── admin/          # GateKeeper — announcements, platform config
│   ├── club-lead/      # Club lead dashboards, events, analytics, messages
│   ├── committee/      # Committee dashboard, QR check-in
│   ├── dean/           # Dean platform overview and governance
│   ├── professor/      # Professor approvals and reports
│   ├── attend/         # Attendance deep links (self check-in)
│   ├── verify/         # Public TrustMark verification UI
│   └── api/            # All API routes
├── middleware.ts        # RBAC + session refresh — runs on every request
├── components/         # Shared UI: EmptyState, FCM, WelcomePage, etc.
├── lib/                # Supabase clients (browser, server, admin), upload utils
├── store/              # Zustand stores (auth, notifications)
├── hooks/              # Custom React hooks
├── types/              # TypeScript interfaces for all data models
├── supabase/           # DB schema, migrations, edge functions
└── public/             # Static assets, PWA manifest, icons
```

---

## 🚀 Production Phases

| Phase | Goal | Status |
|-------|------|--------|
| **1 — Foundation** | Auth, DB schema, `middleware.ts`, PWA shell | ✅ Complete |
| **2 — EventPass** | Events, registration, QR tickets, club-lead flows | ✅ Complete |
| **3 — ClubHub + PulseAlert** | Club pages, FCM notifications, notifications inbox | ✅ Complete |
| **4 — RollCall** | QR attendance scanning, live attendance counter | ✅ Complete |
| **5 — CertifyMe + ODPass** | PDF generation, email delivery, LinkedIn sharing | ✅ Complete |
| **6 — TrustMark** | Document verification, public `/verify` route | ✅ Complete |
| **7 — VScore** | Participation scoring, grades, percentiles, leaderboard | ✅ Complete |
| **8 — InsightBoard** | Analytics dashboards for Club Leads and Dean | ✅ Complete |
| **9 — Polish + PWA** | Dark mode, loading states, responsiveness, PWA assets | ✅ Complete |
| **10 — Launch** | Vercel deploy, Capacitor config, real user onboarding | 🔵 In Progress |
| **11 — Advanced Club Ops** | Multi-role access, volunteer flows, event duplication | 🔵 In Progress |
| **12 — Global Governance** | Dean directory, suspension, export reports, blackout dates | ✅ Complete |
| **13 — Transactional Trust** | DB-level transaction safety, race condition elimination | ✅ Complete |

**143 total tasks · 125 completed · 87% complete — last audited May 28, 2026**

---

## 📊 Current Status

### ✅ Completed

- **Auth & Onboarding** — Supabase-backed auth with Google OAuth, role-aware redirects, and profile completion guards across all 5 roles
- **Student Portal** — Dashboard, event discovery, waitlist-aware registrations, QR EventPass, certificates with LinkedIn sharing, OD letters, VScore, leaderboard, notifications inbox, direct messages
- **Staff & Administration** — Committee QR scan, Club Lead dashboard, Professor event approvals, Dean governance workspace, Admin GateKeeper
- **Transactional Safety** — `register_for_event_transactional` and `mark_attendance_transactional` with row-level locking
- **Analytics** — Full Recharts dashboards for Club Leads (registrations, attendance, follower growth) and Dean (platform-wide stats, department breakdowns)
- **Public TrustMark** — Instant document validation at `/verify/[uuid]` with no login required
- **Multi-Role Access** — Club Leads and Committee members can switch between student and staff dashboards seamlessly
- **Direct Messaging** — Club Lead ↔ Student native messaging interface
- **PWA** — Standalone 192px/512px icons, service worker caching, offline fallback
- **Native Image Upload** — Avatar, club logo, and event banner upload via Supabase Storage
- **Security Hardened** — Document download routes enforce strict ownership checks; FCM endpoints verify sender role and context

### 🔵 In Progress

- Capacitor Android APK and iOS TestFlight builds
- PWA install prompt trigger and offline indicator UI
- Expanded automated testing (smoke tests added; Jest/RTL unit tests pending)
- Club Lead: custom certificate template upload
- Committee: volunteer application approve/reject flow

---

## 🔐 Source Code

This is a **private repository**. The codebase is not publicly available to protect the platform's intellectual property.

If you are a **recruiter, developer, or college administrator** and want to review the source code or see a live architecture walkthrough:

> 📬 **[harshwardhansingh1507@gmail.com](mailto:harshwardhansingh1507@gmail.com)**

I'm happy to walk through the codebase, architecture decisions, and implementation details over a call.

---

## Built By

**Harsh Wardhan** — B.Tech CSE, SRM University Haryana (2025–2029)

[![Portfolio](https://img.shields.io/badge/Portfolio-harshwardhanportfolio.vercel.app-000000?style=flat-square&logo=vercel&logoColor=white)](https://harshwardhanportfolio.vercel.app)
[![GitHub](https://img.shields.io/badge/GitHub-harshwardhan1507-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/harshwardhan1507)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://linkedin.com/in/harshwardhan1507)

---

*Built for SRM Haryana. Designed to scale.*
