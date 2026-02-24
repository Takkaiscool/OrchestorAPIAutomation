description: Example-driven intelligent fuzzing engine.

---

# Strategy

Start with Swagger example.
Apply controlled mutations.

---

# String Mutations

- Empty string
- Max length
- Over max length
- Special characters
- SQL injection pattern
- XSS payload

---

# Number Mutations

- 0
- Negative
- Maximum boundary
- Overflow
- Decimal if integer expected

---

# Enum Mutations

- Invalid enum
- Case variation
- Null if required

---

# Date Mutations

- Invalid format
- Future date
- Extreme past date

---

# Schema-Aware Rules

If schema defines:
- pattern → violate pattern
- minimum → test minimum-1
- maximum → test maximum+1
- format email → invalid email

---

# Fuzz Modes

- Light
- Medium
- Aggressive

Do NOT fuzz:
- Auth tokens
- System-generated IDs
- Security credentials
