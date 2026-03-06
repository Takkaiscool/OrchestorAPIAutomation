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

For MongoDB deployments, use environment variables:

- MONGO_URI
- MONGO_DB_NAME
- MONGO_AUTH_SOURCE (optional)

Use read-only access for sampling whenever possible.

---

# Validation Strategy

After POST:

SQL: SELECT * FROM table WHERE id = ?
Mongo: findOne({ _id: <id> }) or business-key query

Verify:
- Record exists
- Values match API response

After DELETE:

Ensure record no longer exists OR status flag updated.
For Mongo soft-deletes, validate archival/deleted flags and audit fields.

---

# Data Isolation

- Tag created data with unique identifier.
- Clean up test data if required.
- Avoid corrupting shared environments.

---

# Mongo Sampling Rules for Test Generation

- Sample only non-sensitive fields required for payload patterns.
- Mask PII/secrets before generating test assets.
- Prefer small sample windows with deterministic filters (e.g., active=true, latest records).
- Use sampled values to create valid positive and boundary scenarios, not to copy production secrets.
