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

This orchestrator requires:

- web/fetch
- read/file
- edit/editFiles
- search
- execute/runTests
- execute/createAndRunTask
- read/terminalLastCommand

Execution must strictly follow tools.md governance.

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
