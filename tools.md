description: Complete tool governance configuration for Swagger-driven Playwright automation engine.

---

# Allowed Tools (Full List)

## Browser Control

- /code/openSimpleBrowser
- playwright-mcp/browser_install
- playwright-mcp/browser_navigate
- playwright-mcp/browser_navigate_back
- playwright-mcp/browser_click
- playwright-mcp/browser_type
- playwright-mcp/browser_hover
- playwright-mcp/browser_drag
- playwright-mcp/browser_select_option
- playwright-mcp/browser_file_upload
- playwright-mcp/browser_handle_dialog
- playwright-mcp/browser_console_messages
- playwright-mcp/browser_network_requests

Purpose:
- UI fallback testing
- Swagger UI scraping (if JSON not directly accessible)
- UI-based token generation
- Hybrid UI + API workflows

---

## Execution Tools

- execute/runTests
- execute/runTask
- execute/createAndRunTask
- execute/testFailure
- execute/getTerminalOutput

Purpose:
- Run Playwright
- Run node tasks
- Execute CLI commands
- Handle test failure recovery
- Inspect terminal output

---

## Read Tools

- read/readFile
- read/problems
- read/terminalSelection
- read/terminalLastCommand
- read/getTaskOutput

Purpose:
- Read existing test files
- Read Swagger snapshot
- Inspect compilation errors
- Inspect test failures

---

## Edit Tools

- edit/editFiles

Purpose:
- Generate test files
- Regenerate impacted files
- Create reports
- Update snapshots

---

## Search Tools

- search

Purpose:
- Locate impacted test files
- Find schema usage
- Map endpoint to file

---

## Web Tools

- web/fetch

Purpose:
- Fetch Swagger/OpenAPI JSON

---

# Tool Execution Phases

## Phase 1 — Swagger Acquisition

If JSON URL available:
- Use web/fetch

If only Swagger UI available:
- openSimpleBrowser
- browser_navigate
- browser_network_requests
- extract JSON

---

## Phase 2 — Snapshot + Diff

- read/readFile (load previous snapshot)
- edit/editFiles (store updated snapshot)

---

## Phase 3 — Dependency Graph

No external tools required (internal logic).

---

## Phase 4 — Regeneration

- search (locate impacted files)
- read/readFile (inspect existing test)
- edit/editFiles (regenerate selectively)

---

## Phase 5 — Execution

- execute/createAndRunTask
- execute/runTests

On failure:
- read/terminalLastCommand
- execute/testFailure
- execute/getTerminalOutput

---

# UI Automation Usage Rules

Browser tools must ONLY be used if:

1. Swagger JSON cannot be fetched directly.
2. Auth token must be obtained via UI.
3. Business flow requires UI interaction.
4. Hybrid validation is required.

Browser tools must NOT be used for pure API testing.

---

# Strict Rules

- Never edit before reading.
- Never execute tests before regeneration phase completes.
- Never fetch Swagger twice in same run.
- Never expose tokens or DB passwords.
- Retry only 5xx failures (max 2 times).

---

# Security Constraints

Never log:
- Bearer tokens
- API keys
- DB passwords
- Session cookies

Mask all sensitive headers in logs.
