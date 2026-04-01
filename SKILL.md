# Orchestor API Automation Skill

description: Unified single-file skill that consolidates orchestrator logic, engine modules, and tool governance for intelligent Swagger-driven Playwright API lifecycle automation.

---

> This file merges all previously split markdown modules into one single skill definition.

## Source Modules Consolidated

- orchestrator.md
- tools.md
- 01-agent-core.md
- 02-service-execution.md
- 03-db-integration.md
- 04-test-architecture.md
- 05-dependency-graph-engine.md
- 06-schema-diff-engine.md
- 07-change-impact-analyzer.md
- 08-dynamic-regeneration.md
- 09-openapi-fuzzing-engine.md

---

## BEGIN: orchestrator.md

description: Central orchestration engine for intelligent Swagger-driven API lifecycle automation.

---

# API Automation Orchestrator

## Purpose

This file defines the single execution brain that coordinates:

- Swagger exploration
- Dependency graph construction
- Schema diff detection
- Change impact analysis
- Dynamic test regeneration
- Payload generation
- Fuzz testing
- DB validation
- Playwright execution
- Reporting

The user provides ONLY a Swagger/OpenAPI URL.

Everything else must execute automatically.

---

# Entry Point

Input Required:

- Swagger URL

Optional Inputs:

- Environment name (dev, qa, stage, prod)
- Fuzz mode (light, medium, aggressive)
- Regeneration mode (partial, full)

---

# Execution Pipeline

The orchestrator must execute the following steps IN ORDER.

---
## Tool Dependency Declaration

This orchestrator requires the following tool categories:

Browser Tools:
- /code/openSimpleBrowser
- playwright-mcp/*

Execution Tools:
- execute/runTests
- execute/runTask
- execute/createAndRunTask
- execute/testFailure
- execute/getTerminalOutput

Read Tools:
- read/readFile
- read/problems
- read/terminalSelection
- read/terminalLastCommand
- read/getTaskOutput

Edit Tools:
- edit/editFiles

Search Tools:
- search

Web Tools:
- web/fetch

All tool usage must follow tools.md governance strictly.

---
## Step 1 — Load Swagger

Invoke Swagger Loader Engine.

Responsibilities:

- Fetch Swagger JSON.
- Validate OpenAPI structure.
- Normalize schema.
- Extract:
  - paths
  - components.schemas
  - security schemes
  - examples
  - tags

If Swagger fails validation → STOP execution.

---

## Step 2 — Snapshot & Schema Diff Detection

Invoke Schema Diff Engine.

Actions:

- Compare new Swagger with previous snapshot.
- Detect:
  - Added endpoints
  - Removed endpoints
  - Field changes
  - Required flag changes
  - Enum changes
  - Type changes
  - Security changes

If no previous snapshot:
- Mark as initial baseline run.

Store updated snapshot.

---

## Step 3 — Change Impact Analysis

Invoke Change Impact Analyzer.

Responsibilities:

- Map changed endpoints to test files.
- Classify severity.
- Decide:
  - No regeneration needed
  - Partial regeneration required
  - Full regeneration required

Generate impact report.

---

## Step 4 — Dependency Graph Construction

Invoke Dependency Graph Engine.

Actions:

- Identify emitters (id, uuid, token, referenceId).
- Identify consumers (path params, request body refs).
- Build directed graph.
- Perform topological sort.
- Detect cycles.

Output:

- Execution order
- Flow groups (CRUD lifecycle, auth flow, parent-child flow)

---

## Step 5 — Test Case Planning

Using:

- Graph output
- Swagger schema
- Example payloads

Generate:

tests/API_Test_Cases.md

Include:

- Positive tests
- Negative tests
- Boundary tests
- Auth tests
- Idempotency tests
- Schema validation tests
- Flow-based chained tests

---

## Step 6 — Dynamic Test Regeneration

If regeneration required:

- Regenerate only impacted test files.
- Preserve:
  - Custom assertions
  - DB validations
  - Utility layers
- Do not overwrite manual business logic validations.

If initial run:
- Generate full test suite.

---

## Step 7 — Payload Generation & Fuzzing

Invoke Payload Generator + Fuzz Engine.

Actions:

1. Base payload from Swagger example.
2. If example missing → generate from schema.
3. Apply fuzz rules based on selected mode:
   - Light
   - Medium
   - Aggressive

Do NOT fuzz:

- Auth tokens
- Security credentials
- System-generated IDs

---

## Step 8 — DB Validation Integration

If DB configuration exists:

- Attach DB validation hooks.
- After POST:
  - Validate record exists.
- After DELETE:
  - Validate record removed or flagged.
- After UPDATE:
  - Validate updated values persisted.

If DB config not present:
- Skip DB validation safely.

---

## Step 9 — Test Execution

Run Playwright tests.

Retry Policy:

- Retry 5xx errors (max 2).
- Do not retry 4xx.

Generate:

- HTML report
- Coverage summary

---

## Step 10 — Reporting

Generate:

- Swagger_Impact_Report.md
- Coverage_Summary.md
- Flow_Execution_Map.md

Include:

- Total endpoints
- Total tests generated
- Regenerated tests count
- Fuzz tests executed
- DB validations performed
- Failed endpoints
- Schema drift summary

---

# Execution Modes

## Initial Run

- No snapshot exists.
- Generate full suite.
- Store baseline Swagger snapshot.

## Incremental Run

- Compare Swagger.
- Regenerate impacted tests only.

## Major Version Change

If major version detected:

- Perform full regeneration.
- Archive previous tests.

---

# Failure Handling

If:

- Swagger invalid → abort.
- Dependency cycle unresolved → mark advanced flow.
- Schema change critical → block execution until regeneration complete.

Never proceed with inconsistent schema.

---

# Security Rules

- Never log secrets.
- Never print tokens.
- Mask DB credentials.
- Mask auth headers.

---

# Final Guarantee

User interaction model:

User provides:
Swagger URL

System performs:
Complete API lifecycle validation automatically.

No manual per-module execution required.
No manual chaining required.
No manual diff detection required.

Single orchestration pipeline controls everything.

## END: orchestrator.md

---

## BEGIN: tools.md

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

## END: tools.md

---

## BEGIN: 01-agent-core.md

description: Core capabilities for intelligent Swagger-driven API automation using Playwright.

---

# Agent Core Responsibilities

## 1. Swagger Intelligence Mode (MANDATORY FIRST STEP)

Before generating any test cases or scripts, the agent MUST:

- Load and parse Swagger/OpenAPI specification.
- Extract:
  - All endpoints and HTTP methods
  - Request schemas
  - Response schemas
  - Required vs optional fields
  - Example payloads
  - Response examples
  - Error response structures
  - Authentication mechanisms
  - Security schemes
  - Tags and logical groupings

NO test generation is allowed before this analysis completes.

---

## 2. Authentication Detection

Automatically detect authentication type:

- Bearer Token
- OAuth2
- API Key
- Basic Auth
- Kerberos
- Custom headers

Never assume authentication type.
Never hardcode credentials.
Always use environment variables.

---

## 3. Example-Driven Payload Generation

When generating payloads:

1. Prefer Swagger examples.
2. If examples not present:
   - Generate from schema.
   - Populate all required fields.
   - Respect:
     - format
     - enum
     - pattern
     - minimum/maximum
     - minLength/maxLength

### Value Generation Rules

- string → realistic string
- uuid → valid UUID
- email → valid email format
- date-time → ISO 8601
- enum → valid enum value
- integer → valid boundary-aware number

Never send null unless schema allows it.

---

## 4. Test Strategy Matrix

For every endpoint:

- Positive test
- Negative test
- Boundary test
- Authentication test
- Schema validation
- Idempotency validation (PUT, DELETE)

---

## 5. Assertion Rules

Always validate:

- Status code
- Response schema structure
- Required fields
- Critical business fields
- Error schema for negative scenarios

Avoid blind deep equality checks.

---

## 6. Environment Governance

Use environment variables for:

- Base URL
- Auth credentials
- DB credentials
- API keys

Never embed secrets in code.

## END: 01-agent-core.md

---

## BEGIN: 02-service-execution.md

description: Service-specific execution engine and workflow resolution.

---

# Swagger Exploration Phase

1. Load Swagger.
2. Group APIs by:
   - Tags
   - Resource names
3. Categorize:
   - CRUD flows
   - Business workflows
   - Public vs secured APIs

---

# Workflow Graph Construction

Build dependency graph:

POST /users → returns userId  
GET /users/{userId}  
PUT /users/{userId}  
DELETE /users/{userId}

Detect:

- ID emitters
- ID consumers
- Token generators
- Cross-resource dependencies

---

# Cross-API Chaining

If API1 response contains field required by API2:

- Extract dynamically
- Store in runtime context
- Inject into next request
- Never hardcode IDs

---

# Business Flow Testing

If Swagger implies sequence:

CreateUser → Login → AccessProfile

Execute full chain and validate state transitions.

---

# Test Case Grid

Generate:

tests/API_Test_Cases.md

| TC_ID | Flow | Endpoint | Method | Scenario | Depends On | Expected | Auth | Status |

---

# Script Generation Rules

- One test file per resource group.
- Shared utilities extracted.
- Setup and teardown included.
- DB validation included where applicable.

## END: 02-service-execution.md

---

## BEGIN: 03-db-integration.md

description: Database validation and integrity verification layer.

---

# DB Access Rules

Purpose:

- Validate API operations
- Confirm persistence
- Confirm deletions
- Verify referential integrity

Never bypass business logic via DB.

---

# Configuration

Use environment variables:

- DB_HOST
- DB_PORT
- DB_USER
- DB_PASSWORD
- DB_NAME

Never hardcode DB credentials.

---

# Validation Strategy

After POST:

SELECT * FROM table WHERE id = ?

Verify:
- Record exists
- Values match API response

After DELETE:

Ensure record no longer exists OR status flag updated.

---

# Data Isolation

- Tag created data with unique identifier.
- Clean up test data if required.
- Avoid corrupting shared environments.

## END: 03-db-integration.md

---

## BEGIN: 04-test-architecture.md

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

## END: 04-test-architecture.md

---

## BEGIN: 05-dependency-graph-engine.md

description: Graph-based API dependency resolution engine.

---

# Node Model

Node:
  endpoint
  method
  requestSchema
  responseSchema
  emits[]
  consumes[]

---

# Edge Rule

Create edge when:

NodeA.emits ∩ NodeB.consumes ≠ ∅

---

# Detection Rules

Detect emitters:
- id
- uuid
- token
- referenceId
- orderId
- userId

Detect consumers:
- Path params
- Request body references
- $ref schema reuse

---

# Execution Order

- Build directed graph.
- Perform topological sort.
- If cycle detected:
  - Flag as advanced flow.
  - Require seed data.

---

# Flow Types

- CRUD lifecycle
- Parent-child relationship
- Auth-token chain
- Multi-step transactions

## END: 05-dependency-graph-engine.md

---

## BEGIN: 06-schema-diff-engine.md

description: Swagger schema diff detection engine.

---

# Baseline Snapshot

Store:

- Paths
- Methods
- Schemas
- Required fields
- Enum values

---

# Detect Changes

- Endpoint added
- Endpoint removed
- Method removed
- Field added
- Field removed
- Required flag changed
- Type changed
- Enum modified

---

# Severity Matrix

| Change Type | Severity |
|------------|----------|
| Field removed | Critical |
| Required added | Critical |
| Type changed | High |
| Enum removed | High |
| Optional field added | Medium |
| New endpoint | Low |

## END: 06-schema-diff-engine.md

---

## BEGIN: 07-change-impact-analyzer.md

description: Maps Swagger changes to test impact.

---

# Mapping Strategy

Each test maps to:

- Endpoint
- Method
- Schema fields used

---

# Impact Rules

If:

- Field removed → mark test invalid
- Required added → regenerate payload
- Endpoint removed → archive tests
- Enum changed → regenerate enum tests

---

# Output

Generate:

tests/Swagger_Impact_Report.md

| Change | Affected Tests | Severity | Action Required |

## END: 07-change-impact-analyzer.md

---

## BEGIN: 08-dynamic-regeneration.md

description: Automatic regeneration of impacted tests.

---

# Trigger Conditions

- New endpoint
- Required field added
- Schema type changed
- Example modified
- Auth scheme changed

---

# Regeneration Rules

- Preserve custom assertions.
- Regenerate only impacted files.
- Preserve DB validations.
- Preserve utility layers.

---

# Modes

- Partial regeneration
- Full regeneration (major version change)

## END: 08-dynamic-regeneration.md

---

## BEGIN: 09-openapi-fuzzing-engine.md

description: Example-driven intelligent fuzzing engine.

---

# Strategy

Start with Swagger example.
Apply controlled mutations.

---

# String Mutations

- Empty string
- Max length
- Over max length
- Special characters
- SQL injection pattern
- XSS payload

---

# Number Mutations

- 0
- Negative
- Maximum boundary
- Overflow
- Decimal if integer expected

---

# Enum Mutations

- Invalid enum
- Case variation
- Null if required

---

# Date Mutations

- Invalid format
- Future date
- Extreme past date

---

# Schema-Aware Rules

If schema defines:
- pattern → violate pattern
- minimum → test minimum-1
- maximum → test maximum+1
- format email → invalid email

---

# Fuzz Modes

- Light
- Medium
- Aggressive

Do NOT fuzz:
- Auth tokens
- System-generated IDs
- Security credentials

## END: 09-openapi-fuzzing-engine.md

---

