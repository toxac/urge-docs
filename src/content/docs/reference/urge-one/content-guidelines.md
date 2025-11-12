# Content Guidelines (Urge)

This document describes how content is structured in this repository and how to author new content files. It is derived from the collection schemas in `src/content.config.ts` and the repository content folder layout.

Folders

- `src/content/concepts/` — concept pages (MDX)
- `src/content/exercises/` — exercises (MDX)
- `src/content/challenges/` — challenges (MDX)
- `src/content/milestones/` — milestone overview pages (MDX)
- `src/content/summaries/` — milestone summaries (MDX)

High-level contract

- Files are MDX documents with YAML frontmatter describing metadata.
- Frontmatter fields are validated by the Astro content collections defined in `src/content.config.ts`.
- Use `id` (slug-style string) as the canonical identifier for the file.
- Draft content may be kept outside `src/content` (for example in `content_draft/`), and for drafts set `contentMetaId: null`.

Common frontmatter fields (used across collections)

- `id` (string, required) — slug identifier for the document.
- `contentMetaId` (string | null) — integer/UUID that maps to the CMS/content_meta table. For drafts use `null`.
- `programId` (string, required for most collections) — ID of the program the content belongs to.
- `programName` (string, optional) — human-friendly program name.
- `title` (string, required) — page title.
- `subtitle` (string, optional) — short subtitle.
- `pubDate` (date, required) — publication date (ISO yyyy-mm-dd preferred).
- `updatedDate` (date, optional) — last updated date.
- `description` (string, optional) — short summary used for lists / SEO.
- `sequence` (number, required in many collections) — ordering number within the milestone or content type.
- `language` (enum, default `en`) — one of: `en`, `bn`, `hi`, `kn`, `ne`, `ps`, `si`, `ta`, `te`.
- `version` (number, optional) — semantic internal version.
- `archived` (boolean, optional, default false) — mark content archived.

Progression / navigation

Many content files include `previous` and `next` objects to create a linear flow inside a milestone. The shape is:

previous:
  type: <collection-name>
  id: <id-of-previous>

next:
  type: <collection-name>
  id: <id-of-next>

`type` is typically one of: `milestones`, `concepts`, `exercises`, `challenges`, `summaries`.

Content lifecycle notes

- Drafts: keep draft files under `content_draft/` and set `contentMetaId: null` in frontmatter.
- Publishing: when exporting to the CMS or content_meta table, populate `contentMetaId` with the CMS id.
- Deletion/archival: prefer moving files to `content_draft/deleted/` or marking `archived: true`.

Validation and editing workflow

- Author new files in `src/content/<collection>/` using the frontmatter keys described above.
- Run your local build or Astro content validation (if configured) to validate frontmatter types.
- Keep `id` unique within each collection and use consistent slug casing (kebab-case recommended).

Examples (minimal frontmatter for a concept)

---
# Example `src/content/concepts/example-concept.mdx` frontmatter
id: example-concept
contentMetaId: null
programId: b594dc90-3af0-4d62-9f5e-4b5dccba3fe9
title: Example Concept
pubDate: 2025-11-12
sequence: 1
milestone: milestone-3-evaluating-opportunities
---

Tips and conventions

- Use `milestone` to point to the milestone `id` (a milestone file in `src/content/milestones`), not the file path.
- For exercises and challenges include `concept` property where appropriate to link them to a concept `id`.
- For challenges and exercises use `type` (see type-specific docs) to classify the activity format.
- Keep the `previous/next` chain consistent and avoid loops.
- Use ISO dates for `pubDate` and `updatedDate`.

Where to find the authoritative schema

The authoritative schema is `src/content.config.ts`. If you update collection requirements, update the docs here as well.

---

If you want, I can also add linting steps or a short script to validate frontmatter across `src/content` files automatically.
