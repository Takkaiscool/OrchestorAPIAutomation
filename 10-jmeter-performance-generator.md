description: Swagger-to-JMeter performance test generation engine.

---

# Purpose

Generate a complete JMeter `.jmx` test plan from a user-provided Swagger/OpenAPI URL.

The agent must automatically create performance-ready HTTP samplers for **all discovered endpoints**.

---

# Input

Required:
- Swagger/OpenAPI URL

Optional:
- Base URL override
- Thread count
- Ramp-up seconds
- Loop count
- Throughput target
- Auth token or auth profile

---

# Generation Workflow

## 1. Parse Swagger First

Extract:
- All paths and HTTP methods
- Path/query/header parameters
- Request body schema
- Authentication/security requirements
- Tags for logical grouping

If Swagger cannot be parsed, stop and return a validation error.

## 2. Build JMeter Test Plan Structure

Create:
- Test Plan
- Thread Group (configurable users/ramp-up/loops)
- HTTP Request Defaults (base URL)
- HTTP Header Manager
- Cookie Manager (if needed)
- Optional CSV Data Set Config for dynamic values
- Result collectors (Summary + Aggregate style)

## 3. Generate Endpoint Samplers

For each endpoint+method:
- Add one HTTP Request sampler
- Set path and method
- Add query params and headers
- Add request body (JSON) based on Swagger example or schema-generated payload
- Add assertions:
  - Status code assertions
  - Response-time assertion (if threshold configured)
  - Basic response-content assertion for key fields

## 4. Authentication Handling

Support:
- Bearer token
- API key
- Basic auth
- OAuth2 token passthrough

Never hardcode credentials.
Use variables/properties in the JMX file.

## 5. Correlation and Reuse

When a response field is likely reused (id/token/reference):
- Add JSON Extractor
- Store as variable
- Reuse in downstream samplers where relevant

## 6. Output Artifacts

Generate:
- `performance/<service-name>-all-endpoints.jmx`
- `performance/README.md` with run command and variable setup
- Optional endpoint coverage table

---

# Defaults

If not provided by user:
- threads: 10
- ramp-up: 30s
- loops: 1
- timeout: 30000ms

---

# Validation Checklist

Before returning artifacts, verify:
- Every Swagger endpoint has a sampler
- All required path params are represented
- JSON bodies are valid
- Auth placeholders are variable-driven
- JMX is syntactically valid XML

If any check fails, return a generation error with exact endpoint details.
