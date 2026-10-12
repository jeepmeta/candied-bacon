---
name: wafflr-ux-design
description: Use when designing Wafflr screens, flows, interaction states, visual hierarchy, mobile layouts, microcopy, or Butters mascot reactions.
---

# Wafflr UX and Interaction Design

## Role

Design a fast, understandable, joyful mobile-first experience that turns group indecision into a small shared event. Favor clarity and speed over feature density.

## Experience principles

- The user should know what is happening, why, and what to do next without reading instructions.
- The core flow should be one-screen-at-a-time and avoid long scrolling pages.
- Treat join friction as a product risk: links and QR codes should lead directly into the room; accounts are not required for MVP.
- Use playful details to reward meaningful moments, never to delay them.
- Keep votes private until round closure; never expose individual participant votes in the live UI.
- Make loading, waiting, empty, offline, error, expired, tie, and completed states intentional—not generic afterthoughts.

## Design each flow as states

For every screen, define:
1. Entry condition and user goal.
2. Primary action and secondary actions.
3. Visible room/round status.
4. Loading, disabled, validation, error, and retry states.
5. Behavior on refresh, reconnect, back navigation, and a short viewport.
6. Keyboard and touch considerations.
7. Accessible names, focus order, contrast, and reduced-motion behavior.

## Screen-level guidance

- **Home/create:** lead with the decision and a clear create-room action. Avoid a dashboard full of options.
- **Join/lobby:** show room title, participant presence, and whether the room is collecting choices or voting. Do not require users to understand the whole product first.
- **Choice contribution:** make restaurant name the primary field; cuisine is useful supporting metadata. Prevent accidental duplicate entries and give clear feedback.
- **Deck lock:** show a concise review of choices and make the host's explicit finalization action unmistakable. Warn if fewer than two unique choices remain.
- **Voting:** make the card the focus. Right means approve/yes; left means reject/no. Communicate progress without revealing other participants' choices or scores. Make keyboard alternatives available where appropriate.
- **Reveal/results:** show aggregate scores only after the round closes. Celebrate the winner; if tied, explain that the tied options advance.
- **Expired/cancelled/error:** state what happened and the available next step. Do not leave users in a dead-end spinner.

## Butters and motion

Use the existing Butters SVG expressions and food icon assets if present. Choose expressions by state (welcome, waiting, contribution, voting, tie, winner, error). Animate the surrounding container or swap expressions rather than building a complex character rig. Motion should be quick, purposeful, and skippable/respect reduced-motion preferences. Do not add sound or haptics to MVP.

## Layout constraints

- Mobile-first, touch-friendly controls, readable type, and robust safe-area spacing.
- Fit core screens to the viewport when practical; avoid page scroll. If the viewport is too short or the keyboard is open, allow the relevant panel to adapt without hiding the primary action.
- Keep global design tokens centralized in the existing `styles.css` and respect Tailwind CSS v4 conventions.
- Do not add a second design-token source or ad hoc per-screen palette without justification.

## Microcopy

Use short, conversational copy. Be clear before being clever. Avoid guilt, pressure, or language that shames a participant for not voting. Explain missing ballots and round closure plainly. “Sudden death” can be explored as playful copy, but clarity must remain primary.

## Deliverable checklist

For a UX request, provide the user journey/state map, screen composition, important interaction details, edge cases, and testable acceptance criteria. Separate required behavior from visual polish and speculative enhancements.
