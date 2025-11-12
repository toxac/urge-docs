# Challenges — Authoring Guide

Location

- `src/content/challenges/` — store challenge MDX files here.

Purpose

- Challenges are short, actionable tasks designed to push learners to take real world action.
- They may be time-boxed and are often social or behavioral in nature.

Required frontmatter fields (schema-derived)

- `id` (string) — unique slug identifier for the challenge.
- `contentMetaId` (string or null) — external id; `null` for drafts.
- `title` (string) — the challenge title.
- `pubDate` (date) — publication date.
- `milestone` (string) — milestone id the challenge belongs to.
- `concept` (string) — concept id that the challenge maps to.
- `type` (enum) — one of: `think`, `research`, `create`, `communicate`, `execute`.
- `sequence` (number) — ordering number for the challenge within a milestone.

Optional/Helpful fields

- `subtitle`, `updatedDate`, `description` — standard metadata.
- `hasForm` (boolean) — optional if the challenge includes a form or submission.
- `isOpen` (boolean) — optional, useful for time-limited or rolling challenges.

Authoring tips

- Keep challenges short and concrete. They should have a clear deliverable or measurable action.
- Use social hooks: encourage sharing results, getting feedback, or pairing with an accountability partner.
- Use `sequence` to ensure challenges are presented in the right order for the milestone flow.

Minimal example

---
id: compliment-challenge
contentMetaId: null
title: Give Compliments
pubDate: 2025-11-12
milestone: milestone-1-begin-your-thrilling-new-adventure
concept: ready-to-take-the-leap
type: communicate
sequence: 1
hasForm: false
---

Rendering hints

- Challenges are small by design: keep content short, include an explicit step-by-step action list, and indicate expected time commitment.
- If there is a form or a place to submit evidence, set `hasForm: true` and include the relevant component.

Validation

- Verify `concept` and `milestone` references are valid IDs present in `src/content`.
- Keep `type` values exactly one of the enum values in the config.


