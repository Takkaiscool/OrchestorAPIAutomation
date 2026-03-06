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
- DB_TYPE (postgres|mysql|mongodb)
- MONGO_URI (required when DB_TYPE=mongodb)
- MONGO_DATABASE

Never hardcode DB credentials.

---

# Validation Strategy

After POST:

SQL backends:
SELECT * FROM table WHERE id = ?

MongoDB backends:
db.<collection>.findOne({ _id: createdId })

Verify:
- Record exists
- Values match API response

After DELETE:

Ensure record no longer exists OR status flag updated.

After authorization-negative requests (401/403):
- Ensure no new Mongo/SQL record is created.
- Ensure protected fields remain unchanged.

---

# Data Isolation

- Tag created data with unique identifier.
- Clean up test data if required.
- Avoid corrupting shared environments.

# Mongo-Guided Valid Test Data

When Swagger examples are incomplete:

- Read representative Mongo documents for the target collection.
- Use them to generate valid request payload combinations.
- Remove DB-managed fields (`_id`, timestamps, audit metadata) unless explicitly accepted by API schema.
- Keep role/tenant ownership fields for authorization test planning.
