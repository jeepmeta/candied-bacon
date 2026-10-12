---
name: wafflr-frontend-engineering
description: Use when implementing Wafflr React screens, shared components, routing, feature hooks, optimistic interaction, responsive layouts, or API integration.
---

# Wafflr Frontend Engineering

## Role

Build a modular React + TypeScript + Vite client that renders the authoritative room state and makes the core game feel immediate without duplicating domain logic.

## Architecture

- Follow the existing repository layout and conventions before creating new folders.
- Keep screens composed from focused components; keep components presentational when possible.
- Use feature hooks for room, submission, voting, and result workflows when those patterns already exist.
- Keep API/repository calls in service modules. Components must not directly manipulate Supabase tables or author authoritative phase/score/result fields.
- Use the pure TypeScript engine for deterministic display helpers or validation, but server responses decide authoritative state.
- Keep templates as data/configuration. Avoid branching the whole UI tree by template when card metadata/configuration can express the difference.

## TypeScript and data boundaries

- Type every service response and action payload.
- Validate untrusted server/client input at boundaries (for example with the project's existing Zod setup if installed).
- Model loading, success, empty, error, stale, reconnecting, and expired states explicitly.
- Do not use `any` as a shortcut. Narrow unknown data before use.
- Never put secrets or raw anonymous credentials in logs, URLs, analytics, or rendered debug UI.

## Interaction behavior

- A vote swipe should be saved immediately and safely retryable; do not require an unnecessary final ballot submission step.
- Provide accessible alternatives to drag/swipe interactions, such as explicit approve/reject controls or keyboard actions.
- Prevent accidental duplicate requests in the UI, but rely on backend idempotency for correctness.
- Preserve a participant's local session credential across reloads without treating local state as authoritative. On reload/reconnect, fetch a fresh authorized snapshot.
- Handle a failed vote save with clear feedback and a retry path; never show an unsaved vote as definitively persisted.
- Avoid exposing interim vote counts or other participants' choices during an open round.
- Host controls should be visible only when appropriate, but server authorization remains mandatory.

## Visual and responsive implementation

- Use the existing Tailwind CSS v4 setup and centralized `styles.css` tokens. Do not introduce a parallel global CSS system.
- Design for touch first, support keyboard/focus states, and account for reduced motion.
- Core screens should avoid page scrolling where practical. Test short heights and mobile keyboard scenarios.
- Reuse existing Butters/food assets when available. Keep animation purposeful and fast.
- Do not add sound/haptics or heavy animation to MVP.

## Performance and dependencies

- Prefer existing dependencies and simple browser primitives over adding packages.
- Avoid global state libraries until the current feature needs them.
- Keep room snapshots reasonably small and refetch only on meaningful invalidation/reconnect.
- Do not create a monorepo or move the engine into a separate package without evidence that a second client requires it.

## Definition of done

A frontend change is complete when its behavior is typed, loading/error/empty states are handled, keyboard/touch access is reasonable, narrow and short viewports are considered, tests cover important branches, and no UI path bypasses server authority.
