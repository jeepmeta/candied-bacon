---
name: wafflr-release-engineering
description: Use when preparing changes for merge/release, modifying dependencies or environment configuration, adding Supabase migrations, or reviewing deployment readiness.
---

# Wafflr Release Engineering

## Role

Keep the early Wafflr codebase easy to run, inspect, test, and deploy. Prefer reliable small increments over premature infrastructure.

## Repository discipline

- Read `AGENTS.md`, `README.md`, package scripts, and relevant configuration before changing setup.
- Preserve the existing Vite + React + TypeScript baseline unless a documented requirement calls for a change.
- Keep `.env.example` synchronized with configuration. Add descriptions and safe placeholders only; never commit real credentials.
- Document local setup, migration order, and any manual dashboard configuration.
- Avoid committing generated build output, dependency directories, local databases, tokens, and environment files.
- Keep changes reviewable. Separate unrelated refactors and feature work.

## Dependency policy

Before adding a dependency, check whether the repository already has a suitable package or browser primitive. Explain why a new dependency is needed, its runtime impact, and whether it is required for MVP. Keep dependency versions compatible with the existing toolchain; do not invent version numbers.

## Supabase and migrations

- Treat migrations as versioned, forward-only changes unless the repository has an explicit rollback convention.
- Include required indexes, constraints, grants, RLS policies, and tests with schema changes.
- Never assume that enabling RLS alone makes a table secure; inspect every read/write path and function.
- Keep service-role credentials server-side only.
- Test migrations against a clean local/test database when possible. Document checks that cannot be run.
- Use transactional server-side operations for critical game transitions.

## Deployment readiness

Check:
- Build and typecheck are clean.
- Required environment variables are documented and validated.
- No secret is included in client bundles or logs.
- Room creation/joining, voting, tie resolution, and expiration are covered by tests.
- Realtime reconnect falls back to an authorized snapshot fetch.
- Mobile viewport, keyboard, accessibility, and reduced-motion behavior have been reviewed.
- Error reporting does not leak participant credentials or private votes.

## Reporting

When summarizing work, state what changed, why, tests actually run, tests not run, known limitations, and any required manual configuration. Do not describe a scaffold or prototype as production-ready.
