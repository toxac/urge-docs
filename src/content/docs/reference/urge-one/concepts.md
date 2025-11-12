---
title: Concepts
description: Details related to concepts related content
---

# Concepts — Authoring Guide

Location

- `src/content/concepts/` — store concept MDX files here.

Purpose

- Concepts are educational or explanatory pages that teach a topic, provide frameworks, or introduce a set of ideas inside a milestone.
- They are typically read-only content (no form) and are used to structure the learning flow.

Required frontmatter fields (schema-derived)

- `id` (string) — unique slug identifier for the concept.
- `contentMetaId` (string or null) — external content id. Set to `null` for drafts.
- `programId` (string) — required program identifier.
- `title` (string) — visible title of the concept.
- `pubDate` (date) — publication date.
- `sequence` (number) — ordering number for the concept inside its milestone.
- `milestone` (string, reference to `milestones` collection) — the milestone id this concept belongs to.

Optional/Helpful fields

- `subtitle` — short subtitle.
- `updatedDate` — date of last update.
- `description` — short description used in lists and meta.
- `previous` / `next` — progression objects for constructing the reading order. Shape:
  previous:
    type: <collection>
    id: <id>
  next:
    type: <collection>
    id: <id>
- `coverImage` — object with `alt`, `src`, `caption` and `credits` when a cover image is needed.
- `version`, `language`, `archived` — standard metadata fields.

Best practices

- Use kebab-case for `id` (e.g., `understanding-customer-needs`).
- Keep `sequence` consistent: typically small integers 1..N for the milestone.
- Point `milestone` to the milestone `id` (for example `milestone-3-evaluating-opportunities`).
- If you update content substantially, bump `updatedDate` and consider incrementing `version`.
- Use `previous`/`next` to build a smooth flow. The `next` of the milestone may point to the first concept in the milestone.

Minimal example

---
id: understanding-customer-needs
contentMetaId: null
programId: b594dc90-3af0-4d62-9f5e-4b5dccba3fe9
title: Understanding Customer Needs
pubDate: 2025-11-12
sequence: 1
milestone: milestone-3-evaluating-opportunities
---

When to create a concept

- When introducing a new idea, framework, or background reading that helps a learner progress through a milestone.
- Concepts should be evergreen and referenceable by exercises and challenges.

Linking

- Exercises and challenges reference `concept` using the concept `id` (see exercises/challenges docs).
- Keep links stable: if you rename a concept `id`, update all references.
