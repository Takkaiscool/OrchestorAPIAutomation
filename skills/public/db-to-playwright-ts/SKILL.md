---
name: db-to-playwright-ts
description: Convert existing Java JDBC, SQL-driven validation scripts, and database utility code into Playwright TypeScript support code. Use when migrating DB verification helpers, setup/teardown SQL scripts, result-set assertions, or end-to-end tests that need database checks in a Playwright TypeScript project.
---

# DB to Playwright TS

Convert database setup and verification logic into Playwright-compatible TypeScript helpers without smuggling JDBC-era patterns into the new stack.

## Quick workflow

1. Identify the current database access style: raw JDBC, DAO helpers, SQL files, or assertion wrappers.
2. Decide whether the migrated DB code belongs in fixtures, standalone helpers, or pre/post-test setup scripts.
3. Keep SQL readable and parameterized; isolate connection lifecycle and environment secrets.
4. Read `references/db-migration-map.md` for JDBC-to-Node patterns and `references/data-strategy.md` for fixture and cleanup guidance.
5. Return the migrated TypeScript helper modules, sample usage from Playwright tests, and notes about driver packages or transaction assumptions.

## Migration rules

- Replace JDBC connection management with a Node database client suitable for the target engine.
- Keep DB access behind focused helper functions such as `fetchUserById`, `seedOrder`, or `cleanupTestData`.
- Parameterize SQL; do not build string-concatenated queries.
- Prefer fixture-scoped setup/cleanup when tests need deterministic data.
- Limit DB assertions to business-relevant verification and avoid over-coupling tests to incidental schema details.
- If the original Java scripts perform both API/UI actions and DB checks, keep DB concerns in helper modules and call them from the Playwright test only where needed.

## Output shape

Default to this structure unless the repository already has a stronger convention:

- `db/client.ts` for connection creation.
- `db/queries/*.ts` for focused query helpers.
- `fixtures/db-fixtures.ts` for seeded data or cleanup wiring.
- `tests/**/*.spec.ts` for business-level checks that may call DB helpers.

## Decision points

- If the source uses reusable SQL files, keep them as tagged strings or `.sql` assets based on repository preference.
- If the suite relies on transactions for cleanup, expose begin/rollback helpers or use seeded record identifiers for explicit teardown.
- If DB checks are only supporting API tests, prefer API-first assertions and use the DB only for critical state verification.
- If multiple databases are involved, create one helper layer per engine instead of a generic abstraction that hides everything.

## Deliverables checklist

- Migrate JDBC-era helpers into clear async TypeScript modules.
- Preserve deterministic setup and cleanup behavior.
- Keep credentials and environment handling externalized.
- Document any required npm driver package and unverified SQL dialect assumptions.
