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
