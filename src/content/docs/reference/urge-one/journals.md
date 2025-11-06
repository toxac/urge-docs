---
title: Journals
description: User created journals
---

## Overview
The Journal feature enables users to create, edit, view, and manage journal entries tied to their entrepreneurial journey. It supports multi-step progressive forms, rich entry data categories, CTAs (Call To Actions), cross-posting, and emailing followers.

---

## Routes / Pages

### 1. `/assets/journal`
- Displays a list of all journals for the current user.
- Uses client load to fetch journals from the store.
- Renders `JournalList` and individual `JournalCard`s.

### 2. `/assets/journal/form`
- Initializes the multi-step `JournalForm` component.
- Accepts props:
  - `userId`: string (required)
  - `journalId`: string (optional, for editing existing journal)
  - `category`: string (optional, prefill category in new journal)
  - `programRef`: object (optional, links journal to program content)
  - `content_type` and `content_id` (optional)

### 3. `/assets/journal/detail/:journalId`
- Journal Details page.
- Shows a single journal entry's full view, including all base data, entry data, CTA info.
- Uses `journalId` route param to fetch journal from nanostore or backend.
- Supports editing via a link to `/assets/journal/form?journalId=...`

---

## Routing and Query Params

### Base Routes:
- `/assets/journal` — List all journals.
- `/assets/journal/form` — Create new journal or edit existing.
- `/assets/journal/detail/:journalId` — View journal detail.

### Query Parameters for `/assets/journal/form`:
- `journalId` (optional): loads the specified journal for editing.
- `category` (optional): preset journal category when creating a new journal.
- `content_type` & `content_id` (optional): links journal to specific program resource.

---

## Usage Patterns

### Prefilled Journal Creation Links

You can create links to allow users to start journal creation for specific program content with prefills: /assets/journal/form?category=reflection&content_type=module&content_id=abc123

- The `JournalForm` reads these query params.
- Uses `category` to preset the entry data form.
- Uses `programRef` object (content_type, content_id) for deep linking journal to program module/exercise/resource/etc.
- Users get a focused journal creation experience tied to specific learning or activity.

### Editing Existing Journals
*/assets/journal/form?journalId=xyz789*
- `JournalForm` loads existing journal with id `xyz789` from store.
- Pre-fills all form steps with stored data for seamless editing.
---

## Components

### 1. `JournalForm`
- Manages multi-step user flow with steps:
  - **BaseDataForm**: Basic journal info (title, category, content, tags, urgency, etc.)
  - **EntryDataForm**: Category-specific data entry form (Reflection, Build, Market, Money)
  - **CTAForm**: Call To Action details and email notification options

- Props:
  - `userId: string`
  - `programRef?: ProgramReference`
  - `journalId?: string`
  - `category?: string`

- Behaviors:
  - Uses nanostore to load existing journal for editing
  - Maintains signal `journalDraft` to store combined form data
  - Dynamically renders entry data form based on selected category
  - Handles form submission and journal create/update via store/backend
  - Integrates cross-post to blog toggle and follower email toggle
  - Supports cancelling or stepping backward

### 2. `BaseDataForm`
- Inputs for title, content, category, type, urgency, tags
- Uses Felte + Zod for validation and form state
- Emits validated data via `onNext` prop

### 3. Category Entry Data Forms
- `ReflectionForm`, `BuildForm`, `MarketForm`, `MoneyForm`
- Each captures structured data tailored to that journal category
- Use Felte + Zod for schema-driven validation
- Accept `initialData` and emit updates with `onNext`

### 4. `CTAForm`
- Manages Call To Action details (type, title, desc, deadline, email follow-up)
- Toggle to enable/disable CTA details entry
- Validated forms and live toggle control UI visibility

---

## Component Usage Summary

| Component       | Props                                     | Description                                      |
|-----------------|-------------------------------------------|-------------------------------------------------|
| `JournalForm`   | `userId`, `journalId?`, `category?`, `programRef?` | Manages multi-step form flow and state for journal creation/editing. Initializes from query params or prop data. |
| `BaseDataForm`  | `initialData`, `onNext`                   | Collects base journal metadata and content.     |
| `EntryDataForm` | `category`, `entryData`, `onNext`         | Dynamically renders category-specific detail form. |
| `ReflectionForm`, `BuildForm`, `MarketForm`, `MoneyForm` | `initialData`, `onNext`          | Category-specific forms for detailed data entry. |
| `CTAForm`       | `initialData`, `onNext`                   | CTA creation and email notification options.    |
| `JournalList`   | `userId`                                  | Lists all journals of a user.                    |
| `JournalCard`   | `journal`                                 | Displays summary info for a single journal.     |
| `JournalDetail` | `journalId`                               | Shows full detail of a journal post.             |

---

## Typical Journeys

1. **User clicks "Add Journal" button:**

- Navigates to `/assets/journal/form`
- (Optional) with query params to prefill (`category`, `content_type`, `content_id`)

2. **User creates or edits journal using `JournalForm`:**

- Progressively fills base data, entry data, and CTA.
- Saves journal which updates store and triggers notifications.

3. **User views individual journal on `/assets/journal/detail/:journalId`:**

- Views full journal and CTA details.
- Options to edit or cross-post.

4. **User views list on `/assets/journal`:**

- Browses all journals.
- Can filter by category or content via UI.

---

## Schema

### `user_journals` Table (Key Fields)

| Field                 | Type                | Description                      |
|-----------------------|---------------------|--------------------------------|
| `id`                  | string (UUID)       | Unique journal ID               |
| `user_id`             | string              | Owner user ID                  |
| `title`               | string (nullable)   | Journal title                  |
| `content`             | string (nullable)   | Journal main content           |
| `category`            | string (nullable)   | Reflection, Build, Market, Money |
| `type`                | string (nullable)   | Rant, Appeal, Observation, etc.|
| `urgency`             | string (nullable)   | urgency level (low, medium, high) |
| `tags`                | string[] (nullable) | Array of tags                  |
| `entry_data`           | Json (nullable)     | Structured category entry data  |
| `has_cta`             | boolean (nullable)  | Whether CTA is enabled         |
| `cta_type`            | string (nullable)   | Type of CTA                   |
| `cta_title`           | string (nullable)   | CTA title                    |
| `cta_description`     | string (nullable)   | CTA description             |
| `response_deadline`   | string (nullable)   | CTA response deadline           |
| `should_email_followers` | boolean (nullable)| Send email notification to followers |
| `cross_post_to_blog`  | boolean (nullable)  | Crosspost journal to blog       |
| `program_ref`         | Json (nullable)     | Reference to program content { content_id, content_type } |
| `created_at`          | string (timestamp)  | Creation time                  |
| `updated_at`          | string (timestamp)  | Last update time              |

### `ProgramReference` Type

```ts
export type ProgramReference = {
    content_id: string;
    content_type: 'concept' | 'exercise' | 'milestone' | 'challenge';
};
```

---

## Behaviors and Use Cases

### Creating a New Journal
- User navigates to `/assets/journal/form`.
- Fills out multi-step form starting from base data.
- Selects category, fills entry data accordingly.
- Optionally adds a CTA.
- Saves journal entry.
- System enqueues email notifications (if any), processes cross-posting.

### Editing Existing Journal
- User loads existing journal by `journalId`.
- Form is populated from nanostore or DB.
- User edits any step's data.
- On save, updates journal and triggers email/cross-post if applicable.

### Crossposting to Blog
- Users can toggle cross-posting.
- System exposes journal content via API or downloadable markdown.
- Users import into diverse blog platforms.

### Emailing Followers CTA Updates
- Optionally email followers about new CTA.
- Emails are queued asynchronously via background worker.
- Uses reliable transactional email provider.

### Form State & Validation
- Child forms manage their own validation with Felte + Zod.
- Parent manages global journal state and step navigation.
- Form data is progressively merged into shared `journalDraft` signal.

---

This architecture supports scalability, modularity, and extensibility for the Journal feature in Urge, enabling personalized, actionable journal updates for entrepreneurial journeys.

---
