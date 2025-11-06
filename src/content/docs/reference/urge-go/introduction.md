---
title: Introduction
description: Urge Mobile App
---

# Urge-Go — Concept & Implementation Plan

> A compact, mobile-first React Native app (Expo) for on-the-go interactions with the existing Urge backend (Supabase).  
> Focus: quick capture (journals, opportunities), lightweight reading, and notifications. Designed to reuse the web app's data and policies.

---

## Assumptions (made to avoid blocked follow-ups)
- We will use **Supabase** (existing schema) as the backend.
- **Expo (managed)** workflow with **Expo Router** for navigation.
- **Email auth** implemented as **magic-link** (recommended for frictionless mobile login).  
  _Note: your scaffold referenced email/password; this plan follows magic-link as the preferred, mobile-friendly default. If you want password flows instead, the plan can be adjusted._
- Journals: **plain textarea** + image uploads (max 1 MB) on mobile.
- Opportunities: **quick-save only** on mobile (minimal fields); full evaluation remains on the web.
- Push tokens will be **kept on device** initially; a server-side push pipeline can be added later.
- App is **online-first** (no offline sync at MVP).

---

## Goals & Success Metrics

**Primary goals**
1. Capture & save journal entries easily (text + image).  
2. Quick-save opportunities with a 1–2 step flow.  
3. Show user-specific notifications and surface key activity.  
4. Keep the mobile UX minimal and fast — capture in ≤ 2 taps.

**Success metrics (first 4 weeks)**
- Time-to-capture (median) < 20s for quick-save opportunity.
- 80% of betatesters can create a journal entry without help.
- App auth success rate > 95% for invited users.
- No sensitive keys committed to repo; CI & EAS configured.

---

## High-level architecture

- **Frontend**: Expo (React Native, TypeScript), Expo Router, React Query (TanStack Query) + optional Nanostores for ephemeral UI state.
- **Backend**: Supabase (existing Postgres schema, RLS enabled), Supabase Storage for uploads.
- **Push**: Expo Notifications for client registration; optional serverless webhook to send push.
- **Build & CI**: GitHub Actions for lint/type/test; EAS for builds & distribution.
- **Monitoring**: (optional later) Sentry, PostHog / Mixpanel.

---

## Core User Flows

1. **Auth & entry**
   - User opens app → magic-link login (email) → Supabase session established → fetch `user_profiles` → show Home.

2. **Quick-save opportunity**
   - From Home FAB or Opportunities tab → open quick-create modal → fill `title`, `short_description` (optional), `discovery_method` (enum) → save → optimistic UI shows saved card.

3. **Create journal**
   - From Home or Journals tab → Create Journal → textarea input + add image(s) (each ≤ 1MB) → save → visible in Journals list and retrievable by web.

4. **Notifications**
   - In-app feed shows system & user-relevant notifications. Push registration kept local; server-side push pipeline added later.

---

## Navigation & Folder Structure (Expo Router)

app/
_layout.tsx # root layout (auth gating)
_loading.tsx # splash while checking auth
+not-found.tsx
(auth)/
login.tsx # magic-link submit / status
magic-link-landing.tsx # deep link landing or instructions
(tabs)/
_layout.tsx # Tabs layout (Home, Journals, Opportunities, Notifications)
home/
index.tsx # Home / dashboard (header with Settings link)
settings.tsx # read-only settings/profile
quick-capture.tsx # modal to choose Journal or Opportunity
journals/
index.tsx # journals list
add.tsx # create journal
[id].tsx # journal detail
edit.tsx # edit journal
opportunities/
index.tsx # opportunities list
add.tsx # quick-create opportunity
[id].tsx # opportunity detail
comment.tsx # add personal comment (optional)
notifications/
index.tsx # feed
[id].tsx # detail


**Note:** Settings is moved to Home header (not a tab).

---

## Data mapping (mobile → existing tables)

- **user_profiles**
  - Read-only on mobile. Used for display & ownership checks.

- **user_journals**
  - Fields (mobile core): `id`, `user_id`, `title?`, `body`, `images[]` (storage paths), `is_public` (boolean), `created_at`, `updated_at`.
  - Client responsibilities: validate image sizes, generate filenames, upload to Supabase Storage, insert row with image URLs.

- **user_opportunities**
  - Mobile quick-save fields (minimal): `id`, `user_id`, `title` (required), `short_description` (optional), `discovery_method` (enum), `image?`, `created_at`.
  - Full evaluation fields left to web.

- **user_notifications / derived**
  - If not present, create `user_notifications` table (or use an events table). Mobile reads user-specific notifications.

- **user_opportunity_comments**
  - Personal notes only (owner-only). Mobile can create comments but not public comments.

---

## Tech stack & libraries (recommended)

- Expo (managed) + EAS
- Typescript
- Expo Router
- Supabase JS client
- @tanstack/react-query
- expo-image-picker + expo-image-manipulator (for resize/compress)
- expo-notifications
- @expo/vector-icons
- react-native-gesture-handler, react-native-reanimated (if UI needs)
- nanostores (optional) for small UI state; prefer React Query for server state
- Jest + React Native Testing Library for unit/component tests
- GitHub Actions (CI)

---

## Sprint Plan (MVP) — deliverables & tasks

### Sprint 0 — Bootstrap (deliverable: repo + env + CI)
**Tasks**
- Initialize Expo project with TypeScript & Expo Router.
- Add lint, prettier, husky pre-commit hooks.
- Configure `.env.example` (no secrets committed).
- Add `lib/supabase.ts` with env-based client init.
- Add GitHub Action: `yarn lint && yarn test`.

**Acceptance**
- `expo start` works; tests & lint run in CI.

---

### Sprint 1 — Auth + Profile Fetch + Routing (deliverable: auth & landing)
**Tasks**
- Magic-link auth UI & flow (login screen + deep-link landing).
- `hooks/initAuth.tsx` to listen to `onAuthStateChange`.
- Fetch `user_profiles` after login, cache in React Query.
- App-level routing (auth gating & loading state).
- Home header shows profile info; settings accessible from Home.

**Acceptance**
- User logs in with email, confirms via magic link, app loads profile and routes to Home.

---

### Sprint 2 — Journals (deliverable: create/read journals)
**Tasks**
- Journals list screen (paginated).
- Create journal screen with textarea + image upload.
  - Client-side image size check (≤1MB); compress/resize if needed.
  - Upload to Supabase Storage under `user_uploads/{user_id}/{uuid}`.
- Journal detail & edit screens.
- Draft autosave (AsyncStorage) for in-progress entries.

**Acceptance**
- Journal created with images persists and is visible in list and fetchable from web.

---

### Sprint 3 — Opportunities (deliverable: quick-save flow)
**Tasks**
- Quick-create opportunity modal (title + short_description + discovery_method + optional image).
- Opportunities list with quick edit + delete.
- Personal comments per opportunity (owner-only).

**Acceptance**
- Quick-save inserts into `user_opportunities`, lists update optimistically.

---

### Sprint 4 — Notifications & Push plumbing (deliverable: in-app notifications + push registration)
**Tasks**
- Notifications list (reads `user_notifications` or derived events).
- Register device for Expo push notifications (store token locally).
- Client listener for push notifications (foreground background).
- Build serverless webhook pattern or document steps to send pushes later.

**Acceptance**
- Notifications appear in-app; device can register for expo push token and receive a test push (via Expo dev tools).

---

### Sprint 5 — QA & Beta (deliverable: testable beta build)
**Tasks**
- EAS configuration + TestFlight/Play internal track preparation.
- E2E smoke tests, accessibility checks.
- Basic docs: README, env vars, run steps.

**Acceptance**
- Internal test build available; smoke tests pass.

---


