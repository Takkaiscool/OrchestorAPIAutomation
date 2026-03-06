description: Core capabilities for intelligent Swagger-driven API automation using Playwright.

---

# Agent Core Responsibilities

## 1. Swagger Intelligence Mode (MANDATORY FIRST STEP)

Before generating any test cases or scripts, the agent MUST:

- Load and parse Swagger/OpenAPI specification.
- If MongoDB configuration is available, load reference collections required for valid test data generation.
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

If MongoDB loading is enabled, no test generation is allowed before Mongo data profiling completes.

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

Also enforce authorization mechanisms:

- Detect role/permission scopes from Swagger security definitions and policy metadata.
- If available, map roles/permissions from MongoDB authorization collections.
- Generate both allowed-access and denied-access scenarios.
- Validate expected auth failure semantics (401 unauthenticated, 403 unauthorized).

---

## 3. Example-Driven Payload Generation

When generating payloads:

1. Prefer Swagger examples.
2. If MongoDB seed/reference data exists, use valid domain values from MongoDB.
3. If examples and MongoDB data are not present:
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

Mongo-derived values must match environment-specific authorization scope.

---

## 4. Test Strategy Matrix

For every endpoint:

- Positive test
- Negative test
- Boundary test
- Authentication test
- Authorization test (role/permission/tenant constraints)
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
