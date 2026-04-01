---
name: openapi-lifecycle-orchestrator
description: End-to-end Swagger/OpenAPI-driven Playwright API automation orchestration including spec parsing, dependency graphing, schema diffing, impact analysis, dynamic test regeneration, intelligent fuzzing, database validation, and governed tool usage. Use when a user provides an OpenAPI/Swagger URL and expects complete automated API test lifecycle execution with minimal manual input.
---

# OpenAPI Lifecycle Orchestrator Skill

Accept a Swagger/OpenAPI URL as the primary input and execute a full automation lifecycle.

## Required Input
- Swagger/OpenAPI URL.

## Optional Inputs
- Environment name (`dev`, `qa`, `stage`, `prod`).
- Fuzz mode (`light`, `medium`, `aggressive`).
- Regeneration mode (`partial`, `full`).

## Core Rules
- Parse and analyze Swagger/OpenAPI before generating any tests.
- Detect authentication type dynamically; never hardcode credentials.
- Use environment variables for secrets and DB connection values.
- Prefer example-driven payloads; fall back to schema-driven values.
- Preserve custom assertions during regeneration.
- Never bypass business logic via direct DB writes.
- Never fuzz auth tokens, system-generated IDs, or security credentials.

## Tool Governance
Use only the following categories and respect their purpose:

- **Browser tools**: Swagger UI fallback, UI token generation, hybrid UI/API flows.
  - `/code/openSimpleBrowser`
  - `playwright-mcp/browser_install`
  - `playwright-mcp/browser_navigate`
  - `playwright-mcp/browser_navigate_back`
  - `playwright-mcp/browser_click`
  - `playwright-mcp/browser_type`
  - `playwright-mcp/browser_hover`
  - `playwright-mcp/browser_drag`
  - `playwright-mcp/browser_select_option`
  - `playwright-mcp/browser_file_upload`
  - `playwright-mcp/browser_handle_dialog`
  - `playwright-mcp/browser_console_messages`
  - `playwright-mcp/browser_network_requests`

- **Execution tools**: run tests/tasks and inspect failures.
  - `execute/runTests`
  - `execute/runTask`
  - `execute/createAndRunTask`
  - `execute/testFailure`
  - `execute/getTerminalOutput`

- **Read tools**: inspect files, output, and errors.
  - `read/readFile`
  - `read/problems`
  - `read/terminalSelection`
  - `read/terminalLastCommand`
  - `read/getTaskOutput`

- **Edit tools**: generate/regenerate tests and reports.
  - `edit/editFiles`

- **Search tool**: locate impacted code.
  - `search`

- **Web tool**: fetch remote Swagger and related artifacts.
  - `web/fetch`

## Execution Pipeline (Run in Order)
1. **Swagger Intelligence (Mandatory first step)**
   - Extract endpoints, methods, schemas, required/optional fields, examples, error contracts, tags, and security schemes.
2. **Service & Workflow Classification**
   - Group APIs by tags/resources and identify CRUD/business workflows and public/secured boundaries.
3. **Dependency Graph Construction**
   - Build node/edge model from emitted and consumed fields; topologically sort execution order.
4. **Test Strategy Matrix Generation**
   - Generate positive, negative, boundary, and auth tests for each endpoint.
5. **Test Architecture & File Generation**
   - Produce resource-grouped test files and shared utilities.
6. **DB Validation Integration (when applicable)**
   - Validate post-write persistence and delete behavior using read-only verification queries.
7. **Schema Snapshot & Diff Detection**
   - Compare current spec against baseline and classify changes by severity.
8. **Change Impact Analysis**
   - Map Swagger changes to impacted tests and produce actionable impact report.
9. **Dynamic Regeneration**
   - Regenerate only impacted tests in partial mode; use full mode for major changes.
10. **Intelligent Fuzzing**
    - Start from valid example payloads and apply schema-aware mutations per fuzz mode.
11. **Execution, Retry, and Reporting**
    - Execute with retry policy (retry up to 2 times for 5xx only), then publish reports.

## Swagger Intelligence Details
Extract and normalize:
- Paths + methods.
- Request and response schemas.
- Required flags, enum values, format/pattern constraints, min/max constraints.
- Authentication and security schemes.
- Example payloads and response examples.
- Error response structures.

### Authentication Detection
Auto-detect:
- Bearer Token
- OAuth2
- API Key
- Basic Auth
- Kerberos
- Custom headers

Use environment variables for credentials and tokens.

### Payload Generation Rules
1. Prefer OpenAPI examples.
2. If absent, synthesize from schema while satisfying constraints.

Value rules:
- `string` → realistic non-empty string
- `uuid` → valid UUID
- `email` → valid email
- `date-time` → ISO 8601
- `enum` → valid member
- `integer/number` → boundary-aware value

Do not send `null` unless schema explicitly allows `null`.

## Workflow Graph Engine
### Node model
- `endpoint`
- `method`
- `requestSchema`
- `responseSchema`
- `emits[]`
- `consumes[]`

### Edge rule
Create directed edge `A -> B` when `A.emits ∩ B.consumes` is non-empty.

### Detection patterns
Emitters: `id`, `uuid`, `token`, `referenceId`, `orderId`, `userId`.

Consumers:
- Path parameters
- Request-body field references
- `$ref` schema reuse

### Runtime chaining
If API1 returns data required by API2:
- Extract dynamically from response.
- Store in runtime context.
- Inject into downstream request.
- Never hardcode IDs.

If graph cycle is detected, flag as advanced flow and request/derive seed data.

## Test Architecture
Target structure:

```text
tests/
  users/
  orders/
  auth/
  utils/
  schemas/
  db/
```

Shared utilities:
- `authHelper.ts`
- `requestBuilder.ts`
- `responseValidator.ts`
- `dbClient.ts`
- `dataFactory.ts`

Execution:
- Local: `npx playwright test`
- CI: `npx playwright test --reporter=html`

Coverage dimensions:
- Endpoint coverage
- Scenario coverage
- Status-code coverage
- Schema coverage

## DB Integration
Use env vars only:
- `DB_HOST`
- `DB_PORT`
- `DB_USER`
- `DB_PASSWORD`
- `DB_NAME`

Validation examples:
- After `POST`: verify record existence and field correctness.
- After `DELETE`: verify hard deletion or status flag transition.

Data hygiene:
- Tag generated records with unique identifiers.
- Clean up test data as needed.
- Avoid cross-test contamination in shared environments.

## Schema Diff Engine
Maintain a baseline snapshot of:
- Paths
- Methods
- Schemas
- Required fields
- Enum values

Detect:
- Endpoint/method additions or removals
- Field additions/removals
- Required-flag changes
- Type changes
- Enum changes

Severity matrix:
- Critical: field removed, required field added
- High: type changed, enum value removed
- Medium: optional field added
- Low: new endpoint

## Change Impact Analyzer
Map tests to:
- Endpoint
- Method
- Schema fields used

Rules:
- Field removed → invalidate affected tests
- Required added → regenerate payloads/tests
- Endpoint removed → archive/deprecate tests
- Enum changed → regenerate enum coverage

Output:
- `tests/Swagger_Impact_Report.md`

## Dynamic Regeneration
Trigger regeneration on:
- New endpoint
- Required field added
- Schema type changed
- Example payload changed
- Auth scheme changed

Modes:
- **Partial**: regenerate only impacted files
- **Full**: regenerate full suite for major version shifts

Always preserve:
- Custom assertions
- DB validation logic
- Utility abstractions

## Intelligent Fuzzing Engine
Start from valid example payloads and mutate by type and constraint.

String mutations:
- Empty
- Max length
- Over max length
- Special characters
- SQL injection pattern
- XSS payload

Number mutations:
- Zero
- Negative
- Max boundary
- Overflow
- Decimal where integer expected

Enum mutations:
- Invalid member
- Case variation
- Null when required

Date mutations:
- Invalid format
- Future date
- Extreme past date

Schema-aware mutations:
- Violate `pattern`
- Test `minimum - 1`
- Test `maximum + 1`
- Invalid `email` format

Fuzz modes:
- Light
- Medium
- Aggressive

## Outputs to Generate
- `tests/API_Test_Cases.md`
- `tests/Swagger_Impact_Report.md`
- Playwright HTML report from execution

## Minimal Run Checklist
1. Load spec from URL.
2. Parse and classify APIs.
3. Build dependency graph and execution order.
4. Generate/refresh test matrix and scripts.
5. Run tests with retry policy.
6. Run DB validations where configured.
7. Diff schema against baseline.
8. Produce impact report.
9. Regenerate impacted tests (partial/full).
10. Execute fuzz suite according to selected mode.
11. Publish consolidated report artifacts.
