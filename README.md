<div align="center">

<br />

<img src="https://img.shields.io/badge/Next.js-16.2.1-000000?style=for-the-badge&logo=nextdotjs&logoColor=white" />
<img src="https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
<img src="https://img.shields.io/badge/Supabase-Postgres-3ECF8E?style=for-the-badge&logo=supabase&logoColor=white" />
<img src="https://img.shields.io/badge/Deployed-Vercel-000000?style=for-the-badge&logo=vercel&logoColor=white" />
<img src="https://img.shields.io/badge/Status-Live-22c55e?style=for-the-badge" />

<br /><br />

# Vertex — Campus Operating System

### *Where campus life connects.*

**[🔗 Live Demo](https://vertexcampusos.vercel.app)** &nbsp;·&nbsp; **[📬 Request Code Access](mailto:harshwardhansingh1507@gmail.com)** &nbsp;·&nbsp; **[🌐 Portfolio](https://harshwardhanportfolio.vercel.app)**

<br />

</div>

---

## What Is Vertex?

Vertex is a **full-stack campus event and club management platform** built for Indian college students, club leads, and administration. It eliminates the chaos of WhatsApp groups, manual certificates, and paper attendance by bringing everything into one unified system.

This is not a tutorial project. Vertex is a production-grade platform with **5 distinct user roles**, **10 functional modules**, real-time data, transactional database safety, push notifications, PDF generation, QR-based attendance, and a public document verification system — all shipped from a single Next.js codebase to both web and mobile.

> Built solo. Deployed to production. Designed to scale.

---

## What It Does

**For Students**
- Discover and register for campus events
- Get push notifications 1 day and 1 hour before events
- Receive certificates automatically after attending
- Build a VScore — a real-time campus participation grade

**For Committee Members**
- Track upcoming event timelines
- Scan QR tickets to mark live attendance
- Manage assigned event tasks

**For Club Leads**
- Create events and submit them for approval
- Generate and email certificates and OD letters in one click
- Manage committee members and view registration analytics
- Search for students by VScore to invite to the club

**For Professors (In-Charge)**
- Review and approve or reject event proposals from assigned clubs
- Supervise club activities and operations

**For the Dean / HOD**
- View high-level campus analytics
- Monitor total active events, registered students, and active clubs
- Manage global platform configurations and approvals

---

## Feature Modules

| Module | Description |
|---|---|
| **EventPass** | Event discovery, registration, QR ticket generation |
| **PulseAlert** | Firebase push notifications — 24h and 1h reminders before events |
| **ClubHub** | Club profile pages, follow system, club analytics |
| **RollCall** | Live QR-based attendance scanning by committee members |
| **CertifyMe** | Auto certificate generation and email delivery |
| **ODPass** | OD letter generation, auto-sent to student + professor |
| **TrustMark** | Public document verification via unique UUID + embedded QR |
| **VScore** | Real-time campus participation scoring, grades, and leaderboard |
| **InsightBoard** | Analytics dashboards for Club Leads and Dean |
| **GateKeeper** | Dean & Professor controls — approvals, governance, announcements |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Next.js 16.2.1 (App Router) |
| Language | TypeScript 5 |
| Styling | Tailwind CSS v4 |
| Database | Supabase (Postgres + RLS + Edge Functions) |
| Auth | Supabase Auth (SSR, Google OAuth) |
| Storage | Supabase Storage |
| Notifications | Firebase Cloud Messaging (FCM) |
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

Vertex is built as a single Next.js codebase that ships to two platforms — a web app deployed on Vercel and a native Android/iOS app built with Capacitor. The app uses a hosted web view approach where Capacitor loads the live Vercel URL instead of a static export. This means all Next.js features work perfectly including API routes, Supabase SSR, and server components. No code duplication. No separate repos. One codebase, two outputs.

```
               ┌──> Vercel (Web)
               │
Next.js Code ──┤             ┌──> Android
               └──> Capacitor ──┤
                             └──> iOS
```

| Platform | Auth | API Routes | Push Notifications | Camera (QR Scanner) |
|---|---|---|---|---|
| **Web** | Supabase SSR | Next.js API routes | Firebase FCM | Web API |
| **App** | Supabase SSR via web view | Next.js API routes via web view | Capacitor Push plugin | Capacitor Camera plugin |

> **Why not static export?**
> Next.js SSR and API routes are core to how Vertex works. Static exports break many Next.js features. A hosted web view approach keeps the full power of Next.js while wrapping it in native mobile shells.

---

## Architecture

### Role-Based Access Control

Every request passes through `proxy.ts` — a Next.js 16 middleware layer that intercepts the request, validates the Supabase session from cookies, and checks the user's role before any page loads. Wrong role on wrong route → instant redirect.

```
User Request
      ↓
proxy.ts (Next.js Middleware — runs on every matched route)
      ↓
Supabase session check
      ↓
No session         → redirect to /login
Valid session      → role check
      ↓
role = 'student'   → /dashboard, /events, /profile
role = 'committee' → /committee/dashboard, /committee/scan
role = 'club_lead' → /club-lead/dashboard, /club-lead/events
role = 'professor' → /professor/dashboard
role = 'dean'      → /dean/dashboard
      ↓
Wrong role on wrong route → redirect to correct dashboard
```

### Database Layer

```
All queries go through one of three Supabase clients:

supabase.ts (browser client)
  └── Used in Client Components
  └── Respects RLS — user can only see their own data

supabase-server.ts (server client)
  └── Used in Server Components and API routes
  └── Reads session from cookies
  └── Respects RLS

supabase-admin.ts (admin client)
  └── Used only in trusted API routes
  └── Bypasses RLS — for certificate generation, OD delivery
  └── Never exposed to the browser
```

### Transactional Safety

Registration and attendance use PostgreSQL stored functions with row-level locking — preventing race conditions during high-volume simultaneous registrations. Waitlist management, capacity enforcement, and check-in window validation are enforced at the database level, not the application layer.

---

## How Vertex Works — Request Flows

### Authentication Flow
```
User opens app
      ↓
Next.js runs proxy.ts on every matched request
      ↓
Supabase checks session cookie
      ↓
No session → redirect to /login
Valid session → allow through to page
      ↓
Login page → user submits email + password (or Google OAuth)
      ↓
Supabase Auth validates credentials
      ↓
Session cookie set in browser
      ↓
Redirect to correct role dashboard
```

### Event Registration Flow
```
Student clicks Register on an event
      ↓
POST /api/register called with event_id + user_id
      ↓
API route checks:
  ├── Is user authenticated? (Supabase session)
  ├── Is event approved and open?
  ├── Is capacity available? (transactional check with row-level lock)
  └── Has user already registered? (unique constraint)
      ↓
Row inserted into registrations table via stored function
      ↓
QR ticket generated with registration_id
      ↓
Ticket shown in student dashboard + emailed via Resend
```

### Attendance + Document Generation Flow
```
Committee member opens event on day of event
      ↓
Clicks "Scan Attendance" → camera opens (RollCall)
      ↓
Student shows QR ticket → committee scans
      ↓
POST /api/attendance called with registration_id
      ↓
Role-auth check + check-in window enforcement
      ↓
registrations table → attended = true
      ↓
VScore recalculates automatically in real time
      ↓
Club Lead clicks "Generate Certificates + OD Letters"
      ↓
For each attended student:
  ├── pdf-lib generates certificate PDF → stored in Supabase Storage
  ├── pdf-lib generates OD letter PDF → stored in Supabase Storage
  ├── Unique UUID + QR code added to each document
  ├── Resend emails certificate to student
  └── Resend emails OD letter to student + professor
      ↓
Documents available in student dashboard instantly
```

### Document Verification Flow (TrustMark)
```
Professor or recruiter receives a certificate or OD letter
      ↓
Scans QR code on the document
      ↓
Browser opens vertexcampusos.vercel.app/verify/[uuid]
      ↓
GET /api/verify/[id] — queries certificates table by UUID
      ↓
Returns: student name, event, date, club, attended status
      ↓
Public verification page shows VERIFIED or INVALID
      ↓
No login required — fully public endpoint
```

### Push Notification Flow (PulseAlert)
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

---

## 🚀 Production Phases

| Phase | Goal | Status |
|---|---|---|
| **1 — Foundation** | Auth, DB schema, proxy middleware, PWA shell | ✅ Complete |
| **2 — EventPass** | Events, registration, QR tickets, club-lead flows | ✅ Complete |
| **3 — ClubHub + PulseAlert** | Club pages, FCM push notifications, notifications inbox | ✅ Complete |
| **4 — RollCall** | QR attendance scanning, live attendance counter | ✅ Complete |
| **5 — CertifyMe + ODPass** | PDF generation, email delivery | ✅ Complete |
| **6 — TrustMark** | Document verification, public /verify route | ✅ Complete |
| **7 — VScore** | Participation scoring, grades, percentiles, leaderboard | ✅ Complete |
| **8 — InsightBoard** | Analytics for Club Leads and Dean | ✅ Complete |
| **13 — Transactional Trust** | DB-level transaction safety, race condition fixes | ✅ Complete |
| **9 — Polish + PWA** | Loading states, responsiveness, dark mode, PWA assets | 🔵 In Progress |
| **10 — Launch** | Capacitor mobile build, production onboarding | 🔵 In Progress |
| **11 — Advanced Club Ops** | Multi-role access, volunteer flows, event duplication | ⏳ Pending |
| **12 — Global Governance** | Dean export reports, professor analytics | ⏳ Pending |

**114 total tasks · 80 completed · 72% complete as of May 2026**

---

## 📊 Current Status

### Completed

- Supabase-backed auth with role-aware redirects across all 5 roles
- Full student experience — dashboard, events, clubs, registrations, QR tickets, certificates, OD letters, leaderboard, VScore, notifications
- Staff surfaces — committee QR scan, club-lead dashboard, professor approvals, dean dashboard, admin GateKeeper and announcements
- Public verify route and document issuance API
- Full analytics dashboards with Recharts (Club Lead + Dean)
- Transactional registration and attendance with row-level locking and stored functions
- Shared `EmptyState` component for consistent UI across all pages
- `AuthStoreHydrator` syncing Zustand auth store for FCM, follow, and notifications

### In Progress

- PWA icons and install prompt
- Full mobile responsiveness audit
- Loading states and skeleton screens for all pages
- Error handling and toast notifications
- Student profile editing
- Image upload for event banners and club logos
- Dark mode toggle

---

## 🔐 Source Code

This is a **private repository**. The codebase is not publicly available to protect the intellectual property of the platform.

If you are a **recruiter, developer, or college administrator** and want to review the source code or see a live walkthrough:

> 📬 **[harshwardhan1507@gmail.com](mailto:harshwardhansingh1507@gmail.com)**

I am happy to walk through the codebase, architecture decisions, and implementation details over a call.

---

## Built By

**Harsh Wardhan** — B.Tech CSE, SRM University Haryana (2025–2029)

[![Portfolio](https://img.shields.io/badge/Portfolio-harshwardhanportfolio.vercel.app-000000?style=flat-square&logo=vercel&logoColor=white)](https://harshwardhanportfolio.vercel.app)
[![GitHub](https://img.shields.io/badge/GitHub-harshwardhan1507-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/harshwardhan1507)

---

*Built for SRM Haryana. Designed to scale.*
