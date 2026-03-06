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
- MONGO_URI
- MONGO_DB_NAME
- MONGO_COLLECTION_MAP

Never hardcode DB credentials.

---

# Validation Strategy

After POST:

SELECT * FROM table WHERE id = ?

Verify:
- Record exists
- Values match API response

After DELETE:

Ensure record no longer exists OR status flag updated.

---

# Data Isolation

- Tag created data with unique identifier.
- Clean up test data if required.
- Avoid corrupting shared environments.


# Mongo Validation and Test Data Discovery

When MongoDB is configured:

- Read sample documents to derive valid domain-specific test data.
- Build positive test cases from existing valid records before synthetic generation.
- Use role/scope-linked records where available to support authorization test scenarios.
- Prefer read-only lookups for baseline validation and post-operation verification.

Fallback behavior:

- If Mongo configuration is missing or inaccessible, continue with non-Mongo validation strategy without blocking the run.
