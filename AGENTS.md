# AGENTS.md

## Mission

Wafflr is a playful, mobile-first social decision game that helps groups stop waffling and make a decision. The core loop is **Decision → Room → Choices → Deck → Vote → Reveal → Winner**. Restaurants are the first template, not the product's permanent limit.

## Product invariants

- Optimize for the shortest path from “we can't decide” to a satisfying group result.
- Joining through a shared link, room code, or QR code should not require an account or app installation.
- Keep the MVP focused on one polished decision flow. Do not expand scope without a demonstrated user problem.
- The brand is playful, friendly, quick, slightly silly, and polished. The mascot is Butters, a cute waffle character.
- “Stop waffling. Start deciding.” is a positioning candidate, not a locked tagline.
- Do not treat Wafflr as a restaurant directory or recommendation engine. Its reusable core is a decision deck and voting experience.
- No built-in messaging, required user profiles, sound, or haptics for the initial launch.

## UX rules

- Design mobile-first and handle narrow/short viewports and the on-screen keyboard.
- Core game screens should fit within one viewport where practical. Avoid long page scrolling; prefer steps, distinct screens, and modal dialogs.
- Make the next action obvious without instructions.
- Use existing food SVGs and Butters expressions when present. Prefer quick, purposeful Motion animations; avoid elaborate character animation.
- Keep visual tokens and global styling centralized in the existing `styles.css` entry point and follow the project's Tailwind CSS v4 conventions.
- Do not introduce decorative UI that delays the decision or obscures status.

## Architecture rules

- Use the existing React + TypeScript + Vite architecture unless a concrete requirement justifies a change.
- Keep game rules in pure, framework-independent TypeScript under `src/engine/`.
- UI components should render state and dispatch actions; they must not independently implement scoring, tie resolution, authorization, or phase transitions.
- Keep templates data/configuration layered over the shared engine. Avoid template-specific copies of the voting engine.
- Put backend calls behind service/API boundaries. The client is never authoritative for consequential game state.
- Validate external input at boundaries; use explicit types and avoid `any`.
- Do not add a monorepo or major dependency without a clear, documented reason.

## Canonical game rules

- Room phases currently defined by the product contract: `collecting`, `voting`, `round_reveal`, `completed`, `cancelled`, and `expired`.
- Host explicitly finalizes the deck. The voting deck is frozen for that round, and duplicate choices are removed using template-aware normalization.
- Restaurant deduplication should ignore case and insignificant whitespace, but must not merge different branches merely because they share a brand name.
- Voting is approval voting: `true` means approve/yes and `false` means reject/no. A participant may approve any number of cards. An absent ballot contributes no approval; do not fabricate vote rows.
- Votes remain private while a round is open. After closure, show aggregate scores and the outcome, not individual vote identities.
- Snapshot eligible voters at round start. Late joiners do not enter an active round. A participant who leaves remains eligible for that round; a missing ballot counts as zero approvals.
- Closing a round is an authoritative operation. A unique top score wins; tied leaders advance to a smaller tie-breaker deck.
- A persistent tie needs a deterministic, server-assigned fallback order created at initial deck lock. Record how the result was resolved. The precise number of tie-breaker rounds is a product setting, not an implicit assumption.
- Treat expiration as an invariant checked by every server-side operation, not merely by a cleanup job.

## Security and backend boundaries

- Anonymous participant credentials must be random and high-entropy. Store only a server-side hash; return the raw credential over HTTPS and keep it in local storage or an equivalent local session store.
- Never put credentials in room URLs, QR codes, analytics, logs, or realtime events. A room code identifies a room; it is not a credential.
- Bind host permissions to the participant identity, not to possession of a room code alone.
- The server/database must enforce authorization, room phases, voter snapshots, vote privacy, scores, winners, and expiration.
- Use transactions, constraints, idempotency, and concurrency control for deck finalization, vote submission, round closure, and tie advancement.
- Realtime events are notifications, not an authority. Send minimal events after commit; clients refetch an authorized snapshot on reconnect.
- Never expose Supabase service-role credentials to the client.

## Engineering workflow

1. Read the relevant code and tests before editing.
2. State the user problem and smallest complete solution before broad changes.
3. Add or update tests for every game-rule or authorization change.
4. Keep pure engine logic testable without network access or a browser.
5. Run the relevant checks available in the repository (typecheck, unit tests, lint, build, and E2E where applicable). Never claim a check passed unless it was actually run.
6. Document behavior changes and any new environment variables. Never commit secrets.
7. When a product decision is unresolved, label it as a hypothesis or ask rather than silently turning it into a requirement.

## Research standards

- Distinguish verified facts, reasonable inferences, product hypotheses, and open questions.
- Competitors include direct restaurant-decision products and substitutes such as group chats, polls, map apps, randomizers, and ordinary verbal discussion.
- Do not claim Wafflr has no competitors or that the swipe mechanic is unique.
- Recheck changing competitor pricing, usage, rankings, search demand, and market claims before relying on them.
