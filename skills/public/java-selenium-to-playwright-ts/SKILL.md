---
name: java-selenium-to-playwright-ts
description: Convert existing Java Selenium UI automation into idiomatic Playwright TypeScript. Use when migrating Java test classes, Selenium page objects, WebDriver waits, locators, TestNG/JUnit assertions, or shared UI automation utilities to Playwright Test.
---

# Java Selenium to Playwright TS

Convert Java Selenium suites into maintainable Playwright TypeScript with minimal one-to-one translation mistakes.

## Quick workflow

1. Inspect the source suite and identify test framework, page-object usage, custom wrappers, and wait utilities.
2. Preserve test intent first, then rewrite around Playwright primitives instead of mirroring Selenium APIs.
3. Prefer Playwright locators, auto-waiting, fixtures, and web-first assertions over explicit waits or driver helpers.
4. Read `references/migration-map.md` for API mappings and `references/page-object-patterns.md` for target structure before writing code.
5. Produce the migrated TypeScript files plus a short note listing assumptions, unsupported Java helpers, and follow-up cleanup items.

## Migration rules

- Convert `WebDriver` setup and teardown into Playwright Test fixtures or `test.beforeEach` / `test.afterEach`.
- Replace `findElement` / `findElements` with `page.locator()` or nested `locator()` calls.
- Replace `Thread.sleep`, implicit waits, and `WebDriverWait` with Playwright auto-waiting, `expect(locator)` assertions, or event-driven waits.
- Convert page objects to accept `Page` or `Locator` dependencies and expose user-facing actions instead of raw element getters.
- Translate JUnit or TestNG assertions into `expect()` from `@playwright/test`.
- Inline thin Selenium utility wrappers unless they clearly deserve a reusable Playwright helper.
- Keep selectors stable; prefer role, label, placeholder, test id, and accessible-name locators before CSS or XPath.

## Output shape

Return migrations in this general form unless the user asks for another project layout:

- `tests/*.spec.ts` for test cases.
- `pages/*.ts` for page objects or screen models.
- `fixtures/*.ts` for shared login, environment, or seeded state.
- `utils/*.ts` only for cross-cutting helpers that cannot be expressed as fixtures.

## Decision points

- If the Java suite mixes API setup and UI validation, migrate API calls into Playwright `request` fixtures instead of leaving them in UI helpers.
- If a Selenium base class hides driver lifecycle, surface that behavior explicitly in fixtures.
- If locators are brittle XPath chains, convert them to semantic Playwright locators and note any behavior changes.
- If the suite relies on custom retry logic, prefer Playwright retries and trace artifacts.

## Deliverables checklist

- Migrate tests without introducing Selenium-style sleeps.
- Preserve business assertions and coverage.
- Add comments only for non-obvious migration choices.
- Mention any Java utility that could not be translated confidently.
