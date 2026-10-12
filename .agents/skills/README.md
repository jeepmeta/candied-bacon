# Wafflr Agent Skill Library

These project-specific skills give AI coding agents focused procedures for recurring Wafflr work. Read the closest matching skill before substantial changes; combine skills when a task crosses boundaries.

| Skill | Use it for |
| --- | --- |
| [Product strategy](./wafflr-product-strategy/SKILL.md) | MVP scope, feature prioritization, product hypotheses, growth loop |
| [UX design](./wafflr-ux-design/SKILL.md) | Screens, flow design, microcopy, responsive interaction, Butters |
| [Game engine](./wafflr-game-engine/SKILL.md) | Domain rules, deck construction, votes, scores, ties, pure tests |
| [Backend security](./wafflr-backend-security/SKILL.md) | Anonymous identity, Supabase, RLS, transactions, realtime, expiration |
| [Frontend engineering](./wafflr-frontend-engineering/SKILL.md) | React, TypeScript, service boundaries, responsive UI |
| [Quality and testing](./wafflr-quality-testing/SKILL.md) | Unit/integration/E2E strategy, edge cases, release gates |
| [Competitive research](./wafflr-competitive-research/SKILL.md) | Competitors, substitutes, validation, SEO, monetization evidence |
| [Release engineering](./wafflr-release-engineering/SKILL.md) | Dependencies, migrations, environment, deployment readiness |

## Usage notes

- Start with `AGENTS.md` for repository-wide invariants.
- Use skills as focused playbooks, not as permission to ignore the codebase or newer explicit product decisions.
- Treat unresolved details as hypotheses; do not silently promote proposals to requirements.
- If a task changes a canonical product rule, update relevant tests and documentation.
