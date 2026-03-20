---
name: rest-assured-to-playwright-api-ts
description: Convert existing Rest Assured Java API automation into Playwright TypeScript. Use when migrating Rest Assured request builders, authentication filters, response assertions, schema checks, and API test utilities into Playwright Test and its request context APIs.
---

# Rest Assured to Playwright API TS

Convert Rest Assured suites into Playwright TypeScript with clear request helpers, typed payloads, and maintainable assertions.

## Quick workflow

1. Inspect the Java tests for shared `given/when/then` setup, auth filters, serializers, and environment configuration.
2. Move common configuration into Playwright fixtures or helper factories that create `APIRequestContext` instances.
3. Rewrite tests around explicit request calls and `expect` assertions instead of fluent Rest Assured chains.
4. Read `references/api-migration-map.md` for direct mappings and `references/project-patterns.md` for fixture/layout guidance.
5. Return migrated specs plus any required helper modules, typed request bodies, and notes about auth or schema assumptions.

## Migration rules

- Convert `baseURI`, `basePath`, and headers into centralized request fixture configuration.
- Replace Rest Assured filters with helper wrappers or Playwright hooks only when the behavior is still valuable.
- Prefer typed request payload builders and response helper functions over giant fluent chains.
- Validate status, headers, and JSON bodies with Playwright `expect` plus plain TypeScript objects.
- Use Playwright's built-in request context for API-only suites and combine with browser fixtures only when the flow genuinely spans UI + API.
- Preserve idempotency and cleanup behavior; make test data setup explicit.

## Output shape

Default to this structure unless the user specifies another layout:

- `tests/api/*.spec.ts` for migrated API tests.
- `fixtures/api-fixtures.ts` for request context setup and auth bootstrap.
- `clients/*.ts` for domain-level API wrappers when repeated endpoints justify abstraction.
- `models/*.ts` for typed payloads and parsed response shapes.

## Decision points

- If Rest Assured uses POJO serialization, convert the payloads to interfaces/types and plain objects.
- If the Java suite uses response specification builders, replace them with reusable assertion helpers.
- If the tests share OAuth or session bootstrap logic, centralize it in a fixture instead of repeating request setup.
- If schema validation is important, keep it with a lightweight validator library or focused assertion helper.

## Deliverables checklist

- Migrate fluent chains into readable Playwright request code.
- Keep auth, base URL, and common headers centralized.
- Preserve endpoint coverage and negative-path assertions.
- Document any Java-specific custom filter or serializer that needs a follow-up implementation.
