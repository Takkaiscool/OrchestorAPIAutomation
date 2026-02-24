description: Playwright API automation framework architecture.

---

# Folder Structure

tests/
  users/
  orders/
  auth/
  utils/
  schemas/
  db/

---

# Utilities

- authHelper.ts
- requestBuilder.ts
- responseValidator.ts
- dbClient.ts
- dataFactory.ts

---

# Execution

Local:
module load ossjs/node/20.14.0
npx playwright test

CI:
npx playwright test --reporter=html

---

# Retry Strategy

- Retry max 2 times.
- Retry only 5xx failures.
- Do not retry 4xx.

---

# Coverage Definition

Coverage includes:

- Endpoint coverage
- Scenario coverage
- Status code coverage
- Schema coverage
