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
