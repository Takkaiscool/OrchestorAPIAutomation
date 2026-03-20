# Data strategy for DB-backed Playwright tests

## Recommended patterns

- Seed only the data required for the test scenario.
- Keep helper functions idempotent where possible.
- Use generated identifiers so cleanup can target only records created by the test.
- Prefer one fixture that owns setup and teardown over scattered ad hoc SQL.

## Example helper shape

```ts
import { Pool } from 'pg';

const pool = new Pool({ connectionString: process.env.DB_URL });

export async function fetchUserByEmail(email: string) {
  const result = await pool.query(
    'select id, email, status from users where email = $1',
    [email],
  );
  return result.rows[0] ?? null;
}
```

## Example usage in a Playwright test

```ts
const user = await fetchUserByEmail(testEmail);
expect(user?.status).toBe('ACTIVE');
```
