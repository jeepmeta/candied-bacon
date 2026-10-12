---
name: wafflr-backend-security
description: Use when designing or implementing Wafflr Supabase schema, anonymous sessions, authorization, RLS, trusted transitions, realtime events, expiration, or concurrency safety.
---

# Wafflr Backend Security

## Role

Protect anonymous participants and enforce authoritative game behavior at the database/server boundary. Treat the browser as untrusted.

## Trust model

- The server/database is the source of truth for room phase, participant role, eligible voters, deck lock, scores, round outcomes, and expiration.
- The client may request an action; it may not declare that a consequential transition succeeded.
- Use Supabase Postgres and Realtime if they remain the selected stack, but keep the domain contract independent of a specific transport.
- Use trusted Edge Functions and/or transactional SQL functions for consequential actions. RLS alone is not a substitute for transactional game rules.

## Anonymous identity

- Generate a cryptographically secure, high-entropy participant credential.
- Store only a hash of the credential server-side. Return the raw token only over HTTPS and store it locally for rejoin.
- Never place the credential in a URL, QR code, room code, analytics payload, error report, log, or Realtime event.
- A room code is an identifier, not proof of identity.
- Bind host authority to the host's participant identity and verify it for every host-only operation.
- Validate membership and role on every operation. Apply rate limits to room creation, joining, and credential guessing.

## Authoritative operations

At minimum, protect:
- Room creation/join/rejoin/cancel.
- Host and participant choice submission.
- Deck finalization and immutable round-choice creation.
- Round start and eligible-voter snapshot.
- Vote creation/revision and round closure.
- Aggregate scoring, tie advancement, fallback resolution, and final result.
- Expiration and deletion/retention rules.

For each operation validate credential, room membership, role, current phase, payload, idempotency key where appropriate, and expiration. Return typed, non-sensitive errors.

## Transactions and data integrity

- Use transactions and row locks or an equivalent concurrency strategy for deck finalization, round closure, tie advancement, and result creation.
- Add unique constraints for room codes, credential hashes, round numbers per room, and one vote per participant/choice/round.
- Make retries idempotent so double taps, reconnects, and network retries cannot create duplicate rounds or results.
- Keep draft submissions editable before lock, but freeze the round's choices after lock.
- Store fallback ranks when the initial deck is locked; do not calculate a new fallback from client input at the end.

## Vote privacy and Realtime

- During an open round, a participant may read their own ballot but not other participants' votes or interim totals.
- After closure, reveal aggregate scores and outcome without revealing vote-to-person mappings.
- Realtime payloads should be minimal notifications emitted after transaction commit. Do not broadcast raw database rows or credential-related fields.
- Realtime is not authoritative. On initial load, reconnect, or version mismatch, refetch an authorized room snapshot.
- Do not let realtime presence or arbitrary polling extend room lifetime.

## Expiration and retention

- Check inactivity and maximum lifetime on every trusted operation.
- Cleanup jobs may remove expired records, but correctness must not depend on cleanup running on time.
- Make inactivity TTL, maximum room lifetime, and completed-result retention configurable.
- Proposed defaults such as two-hour inactivity or 24-hour maximum lifetime are starting points, not fixed product decisions.

## Schema review checklist

Review ownership/foreign keys, phase constraints, unique constraints, deletion behavior, RLS for every table, service-role boundaries, indexes for room/round lookups, auditability of result resolution, and privacy of views/functions. Never expose service-role credentials to browser code.

## Required security tests

Test anonymous token guessing resistance at the contract level, host-only authorization, cross-room access denial, vote privacy through direct API and Realtime, no voting after closure, late joiner exclusion, concurrent close/finalize requests, idempotent retries, expiration enforcement, and no credential leakage in URLs/loggable payloads.
