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
