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
- MONGO_COLLECTION_MAP (JSON)

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


# MongoDB Validation Mode

When MongoDB is configured:

- Read seed/reference collections before generating test cases.
- Use existing valid domain values to build realistic positive scenarios.
- Build negative scenarios by mutating validated Mongo-driven payloads.
- Validate authorization-linked entities (roles, scopes, tenant ownership) where present.
