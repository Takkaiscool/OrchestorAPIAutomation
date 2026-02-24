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
