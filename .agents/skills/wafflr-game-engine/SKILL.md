---
name: wafflr-game-engine
description: Use when implementing or reviewing Wafflr room phases, deck construction, normalization, voting, scoring, tie-breakers, deterministic transitions, or engine tests.
---

# Wafflr Game Engine

## Role

Own the correctness of Wafflr's reusable, deterministic decision engine. Keep the engine pure TypeScript and independent of React, Supabase, browser APIs, and presentation details.

## Non-negotiable invariants

- Room phases: `collecting`, `voting`, `round_reveal`, `completed`, `cancelled`, `expired`.
- Host explicitly finalizes the deck. A finalized round's choices are immutable.
- Deck choices are deduplicated before voting using template-aware normalization.
- Approval vote semantics: `true = approve`, `false = reject`. A participant may approve any number of choices.
- Score is the number of eligible participants who approved a choice.
- No vote row is required for an unvoted choice; missing approval contributes zero.
- Votes may be revised only while the round is open.
- Votes and scores are not publicly revealed until the round closes.
- Eligible voters are snapshotted at round start. Later joiners are excluded from that round.
- A unique top score wins. Every tied leader advances to the next reduced deck.
- Persistent ties are resolved by a documented deterministic fallback order assigned by the server at initial deck lock; never use client-controlled order or fresh randomness at resolution time.
- The exact number of tie-breaker rounds must be configurable/documented, not silently hard-coded as an agreed product decision.

## Module boundaries

Keep focused modules under `src/engine/`, for example:
- `room-state.ts` and `transitions.ts`: valid phases and allowed transitions.
- `deck.ts` and `deduplication.ts`: deck construction and template-aware normalization.
- `voting.ts` and `scoring.ts`: ballot validation and aggregate counts.
- `tie-breaker.ts`: tied leader selection and fallback policy.
- `invariants.ts`: assertions for valid state.

Prefer small pure functions that accept explicit inputs and return values or typed domain errors. Never perform I/O from the engine.

## Restaurant deduplication

Normalize insignificant whitespace and compare names case-insensitively. Do not merge distinct locations or branches merely because the restaurant brand matches. If future templates need different identity rules, give each template an explicit normalizer instead of adding restaurant assumptions to generic engine code.

## State-transition discipline

For each transition, define:
- Preconditions (phase, actor permissions as supplied by trusted caller, required data).
- State changes.
- Idempotency behavior.
- Postconditions/invariants.
- Typed failure cases.

The engine can validate domain rules, but authorization must be enforced by the trusted service/backend too.

## Edge cases to cover

- Empty deck and fewer than two unique choices.
- Duplicate submissions with varied casing and whitespace.
- Multiple approvals from one participant.
- Revised vote before close; attempted revision after close.
- Zero-vote tie.
- All participants missing a ballot.
- One eligible voter, late joiner, and participant who leaves mid-round.
- Tie among two or more leaders, including tie-breaker persistence.
- Invalid phase transitions and repeated/idempotent requests.
- Stable fallback rank preserved across retries and restarts.

## Testing expectations

Test pure functions with table-driven cases and deterministic fixtures. Assert exact results and invariants, not implementation details. Add property-style tests when useful, such as: scores are non-negative; no choice scores higher than eligible voter count; a winner is always among top-scoring eligible choices; fallback is stable for the same locked deck. Avoid relying on network, wall-clock time, or nondeterministic random values in unit tests.
