---
title: Squad
description: handling user squad
---

# Urge Cheer Squad Feature Documentation

## Overview

The Cheer Squad feature in Urge enables founders to build a personal support network for accountability, motivation, and encouragement. This documentation describes the architecture, data flow, and component usage for the squad management functionality including member addition, deletion, status tracking, and update communications.

---

## Data Model

### Tables

- **user_cheer_squad**
  - Stores squad member details linked to a user.
  - Key fields: `id`, `user_id`, `name`, `email`, `relationship`, `status`, `created_at`, `updated_at`.

- **user_cheer_squad_updates**
  - Stores update messages sent to squad members.
  - Key fields: `id`, `user_id`, `cheer_squad_id`, `update_text`, `update_link`, `created_at`.

---

## Nanostores

### squadStore

- Holds the list of squad members for the current user.
- Supports initialization, create, update, and delete operations.
- Tracks loading and error states.
- Uses `squadInitialized` atom to avoid redundant fetching.

### squadUpdatesStore

- Holds update messages for the current user's squad members.
- Supports initialization and reactive updates.
- Uses `squadUpdatesInitialized` atom to avoid redundant fetching.

### Initialization Tracking Atoms

- `squadInitialized` and `squadUpdatesInitialized` track whether respective stores have been loaded.
- Prevent repetitive server calls and optimize performance.

---

## Components

### SquadList

- The central component for managing squad members.
- Handles loading initialization of squadStore and squadUpdatesStore together.
- Provides UI to list squad members with status badges.
- Controls modal dialog state with `showModal` and `mode` signals (`list`, `form`, `details`).
- Opens either `SquadDetails` or `SquadForm` in modal.
- Shows combined loading spinner until all data is ready.
- Supports deleting squad members.

### SquadDetails

- Displays detailed info and updates for one selected squad member.
- Reads from fully initialized stores; does not fetch data itself.
- Filters updates that belong to the selected member.
- Renders member's name, relationship, status, and a scrollable list of updates.

### SquadForm (AddSquadMemberForm)

- Form component to add new squad members.
- Uses Felte + Zod for form validation and submission.
- On success, triggers reinitialization or refresh of squad data in `SquadList`.
- Sets default status to `"request pending"` for new members.

---

## Data Flow and User Interaction

1. **Initialization**
   - `SquadList` initializes both `squadStore` and `squadUpdatesStore`.
   - Shows global loading spinner during fetch.
   - After initialization, renders list of members.

2. **Viewing Member Details**
   - Click on a member's 'View details' button in `SquadList`.
   - Opens modal with `SquadDetails` for that member (uses pre-fetched store data).
   - User sees member info and update messages.

3. **Adding a New Member**
   - Click ‘Add Squad Member’ button in `SquadList`.
   - Opens modal with `SquadForm`.
   - User submits form; on success modal closes and squad list refreshes.

4. **Removing a Member**
   - Click ‘Remove’ button on squad member card in `SquadList`.
   - On success, member is removed from store and UI updates reactively.

---

## Styling and Framework Notes

- Components use SolidJS for reactive UI.
- Nanostores maintain global app state.
- Tailwind CSS with Flowbite for styling consistency.
- Iconify used for iconography.
- Modal component handles dialog rendering and accessibility.
- Felte + Zod used for robust form handling and validation.

---

## Best Practices and Design Rationale

- Centralized initialization optimizes data fetching and app responsiveness.
- Nanostores ensure single source of truth for squad data.
- Modal state managed by `SquadList` avoids conflicts from multiple open dialogs.
- Clear separation of concerns between list, details, and form components.
- Loading and error states surfaced prominently for user clarity.
- Validation enforced on squad member addition to maintain data quality.

---

This architecture and implementation ensure the Cheer Squad feature is performant, user-friendly, and maintainable for ongoing evolution of the Urge platform.
