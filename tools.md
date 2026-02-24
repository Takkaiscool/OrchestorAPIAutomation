description: Defines tool access, ordering, and usage rules for the API Automation Orchestrator.

---

# Tool Governance Policy

The agent must use tools in a structured and deterministic order.

No tool may be used outside its defined phase.

---

# Allowed Tools

## 1. web/fetch

Purpose:
- Fetch Swagger/OpenAPI JSON from URL.

Mandatory in Step 1 of orchestrator.

---

## 2. read/file

Purpose:
- Read existing test files.
- Read previous Swagger snapshot.
- Read configuration files.

Must always read before editing.

---

## 3. edit/editFiles

Purpose:
- Create or update:
  - Test files
  - Snapshot files
  - Impact reports
  - Coverage reports
  - Flow maps

Never overwrite without reading first.

---

## 4. search

Purpose:
- Locate existing test files by endpoint name.
- Detect impacted files during regeneration.

---

## 5. execute/runTests

Purpose:
- Execute Playwright tests.
- Trigger CI run.

Must only be used after test generation completes.

---

## 6. execute/createAndRunTask

Purpose:
- Run CLI commands:
  - npx playwright test
  - npm install
  - module load ossjs/node/20.14.0

---

## 7. read/terminalLastCommand

Purpose:
- Inspect failure reason after test execution.

Used only in failure recovery.

---

# Tool Usage Order (Strict)

Step 1:
web/fetch

Step 2:
read/file (load snapshot)

Step 3:
search (locate impacted tests)

Step 4:
edit/editFiles (regenerate tests)

Step 5:
execute/createAndRunTask (run Playwright)

Step 6:
read/terminalLastCommand (on failure only)

---

# Tool Restrictions

- Never fetch Swagger twice in same run.
- Never edit before reading existing file.
- Never execute tests before regeneration phase completes.
- Never log secrets from environment variables.

---

# Error Recovery Rules

If tests fail due to schema mismatch:

1. Read failure logs.
2. Re-check Swagger.
3. Regenerate impacted test only.
4. Re-run.

Maximum retry cycles: 2.

---

# Security Constraints

Never:
- Print DB passwords
- Print Bearer tokens
- Print API keys
- Commit secrets into test files
