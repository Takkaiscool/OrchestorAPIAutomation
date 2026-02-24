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
