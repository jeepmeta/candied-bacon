---
name: wafflr-quality-testing
description: Use when creating a test plan, writing unit/integration/E2E tests, reviewing a Wafflr change, or preparing a release-quality checklist.
---

# Wafflr Quality and Testing

## Role

Test Wafflr as a multi-participant, asynchronous game—not just as a set of screens. Focus on correctness under retries, late arrivals, privacy boundaries, and competing actions.

## Test layers

1. **Pure engine unit tests:** phases, normalization, deck construction, scoring, tie advancement, fallback ordering, and invariants.
2. **Service/database tests:** authorization, constraints, transactions, idempotency, expiration, RLS, and vote privacy.
3. **Component tests:** interaction feedback, accessible controls, loading/errors, and phase-specific rendering.
4. **End-to-end tests:** host creates room, invitees join, choices are submitted, host locks deck, participants vote, results reveal, tie-breaker completes, and winner appears.
5. **Manual responsive/accessibility pass:** narrow width, short height, keyboard open, keyboard navigation, reduced motion, and reload/reconnect.

## Critical scenarios

- Host and participant identities remain distinct; host-only actions cannot be invoked by participants.
- Duplicate choice submissions collapse correctly without merging separate restaurant branches.
- Finalizing a deck twice cannot create duplicate rounds.
- A deck cannot be changed after lock.
- One participant cannot vote twice for the same card in a single round, but can revise a vote before close.
- No participant can vote after closure.
- No interim votes or scores leak through API, cache, or Realtime.
- Eligible voters are snapshotted at round start; late joiners do not alter the denominator.
- Missing ballots count as zero approvals without fake vote records.
- Concurrent round closure produces exactly one result.
- Zero votes and all-equal scores trigger the documented tie policy.
- Fallback winner is stable after retries/restarts and has a recorded resolution type.
- Expiration blocks actions even if cleanup has not run.
- Refresh/reconnect restores room state from the server and preserves anonymous rejoin capability.
- Invalid, expired, cancelled, or missing rooms have clear user-facing states.

## Test design rules

- Use deterministic fixtures and controlled clocks/randomness for engine tests.
- Prefer tests of observable behavior and invariants over component implementation details.
- For concurrency tests, issue overlapping requests and assert one valid committed outcome.
- Include direct API/RLS checks; hiding a control in the UI is not authorization.
- Avoid real external restaurant APIs in deterministic tests.
- Do not call a test passed unless it was executed. If the environment lacks dependencies/services, state what remains unverified.

## Release gate

Before describing a change as ready, run the available equivalents of:
- Typecheck.
- Unit tests.
- Lint.
- Production build.
- Database migration/RLS tests.
- E2E happy path and critical failure paths.

Report results accurately with **passed**, **failed**, and **not run** clearly separated. Never hide a failing test behind a successful build.
