description: Generates JMeter performance test plans directly from a user-provided Swagger/OpenAPI URL.

---

# JMeter Performance Generator

## Purpose

This module enables Copilot to automatically build a `.jmx` test plan that can be executed in Apache JMeter for performance testing of **all Swagger endpoints**.

The user input remains minimal:

- Swagger/OpenAPI URL

No manual endpoint-by-endpoint setup is required.

---

## Inputs

Required:

- Swagger URL

Optional:

- Base environment override (dev/qa/stage/prod)
- Thread count
- Ramp-up time
- Loop count or duration
- Auth token/value placeholders
- Tag filter (to include only specific groups)

---

## Output

Generate:

- `performance/<service-name>-performance.jmx`
- `performance/<service-name>-endpoints.csv` (optional endpoint dataset)
- `performance/Performance_Test_Plan.md`

The generated plan must include every eligible endpoint from Swagger unless a tag filter is applied.

---

## Generation Workflow

### Step 1 — Parse Swagger

Extract:

- base URL / servers
- paths and HTTP methods
- path params
- query params
- header params
- request body schema/examples
- auth/security schemes

Skip endpoints explicitly marked deprecated unless override is enabled.

### Step 2 — Build Request Definitions

For each endpoint+method:

- Resolve final URL template.
- Create parameterized entries for path/query/header values.
- Generate sample request payload from example/schema.
- Attach default assertions:
  - status code check
  - response time threshold check

### Step 3 — Create JMeter Test Plan Structure

Minimum structure:

- Test Plan
- Thread Group
- HTTP Request Defaults
- HTTP Header Manager
- CSV Data Set Config (if generated)
- One HTTP Sampler per endpoint/method
- Response Assertions
- Duration/latency listeners (non-GUI safe defaults)

Recommended naming:

- `<METHOD> <PATH>` for each sampler.

### Step 4 — Auth Handling

Security rules:

- Add placeholders for credentials/tokens.
- Never hardcode live secrets.
- Support bearer/API key/basic auth mappings.

If token endpoint exists in Swagger:

- Generate optional preprocessor/auth sampler flow.

### Step 5 — Dependency-Aware Ordering

Where possible, order samplers using dependency hints:

- POST create before GET by id / PUT / DELETE
- Auth/login before protected routes
- Parent resources before child resources

If dependency cannot be inferred:

- Fall back to deterministic path+method order.

### Step 6 — Save & Report

Persist generated files and produce summary markdown with:

- total endpoints discovered
- total samplers generated
- skipped/deprecated endpoints
- auth assumptions/placeholders
- manual value placeholders requiring user updates

---

## Validation Rules

Before finalizing `.jmx`:

- Ensure XML structure is valid.
- Ensure every included endpoint has a sampler.
- Ensure each sampler has method, path, and host configuration.
- Ensure request body samplers include proper content-type.
- Ensure no secret values are embedded.

---

## Non-Goals

This module does not:

- run load tests automatically in production
- infer strict business SLAs without user input
- generate chaos/stress profiles unless configured

---

## Success Criteria

The feature is successful when a user can provide only Swagger URL and receive a runnable JMeter `.jmx` plan covering all API endpoints for baseline performance testing.
