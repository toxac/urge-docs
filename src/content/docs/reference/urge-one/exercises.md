# Exercises — Authoring Guide

Location

- `src/content/exercises/` — put exercise MDX files here.

Purpose

- Exercises are interactive or reflective activities that ask learners to do work (think, research, create, etc.).
- Exercises may include forms (see `hasForm`), embed components, or client-side interactivity.

Required frontmatter fields (schema-derived)

- `id` (string) — unique slug identifier for the exercise.
- `contentMetaId` (string or null) — external CMS id; `null` for drafts.
- `programId` (string) — program identifier.
- `title` (string) — exercise title.
- `pubDate` (date) — publication date.
- `milestone` (string, reference to milestone id) — which milestone this exercise belongs to.
- `concept` (string, reference to concept id) — the concept id this exercise connects to.
- `type` (enum) — one of: `think`, `research`, `create`, `communicate`, `execute`.
- `sequence` (number) — ordering of the exercise within the milestone or concept.

Optional/Helpful fields

- `subtitle`, `updatedDate`, `description` — standard metadata.
- `hasForm` (boolean) — set to `true` if the exercise includes a form or interactive submission.
- `previous` / `next` — to construct navigation.

Authoring tips

- Use `concept` to point exercises to the concept they reinforce.
- For interactive exercises with forms, set `hasForm: true` and ensure the appropriate front-end component is referenced in the body (for example `<OpportunityForm .../>`).
- Use `type` appropriately: choose the activity behavior. This value is used by the UI to surface exercises by type and to set expectations.
- Keep `sequence` values consistent and non-overlapping within a milestone.

Minimal example

---
id: identifying-your-problems-and-frustrations
contentMetaId: null
programId: b594dc90-3af0-4d62-9f5e-4b5dccba3fe9
title: Problem Finder
pubDate: 2025-11-12
milestone: milestone-2-discovering-opportunities
concept: your-problems
type: think
sequence: 1
hasForm: true
---

Rendering hints

- If `hasForm` is `true`, include the client-side React/TSX component in the MDX body.
- Keep exercises focused: short instructions, clear objectives, and explicit next steps.

Validation

- Ensure `concept` references a valid concept `id`.
- Use the same `programId` across content belonging to the same program.
