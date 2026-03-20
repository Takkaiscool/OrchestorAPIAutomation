# JDBC / SQL to Playwright TypeScript migration map

## Typical stack choices

- PostgreSQL -> `pg`
- MySQL / MariaDB -> `mysql2/promise`
- SQL Server -> `mssql`
- Oracle -> `oracledb`

Choose the driver that matches the existing database instead of inventing a cross-database wrapper too early.

## Core mapping

- `DriverManager.getConnection(...)` -> async client or pool factory in Node
- `PreparedStatement` -> parameterized query call with placeholders matching the driver
- `ResultSet` iteration -> returned rows array mapped to typed objects
- `@Before` / `@After` DB setup -> Playwright fixtures or helper hooks
- SQL assertion helpers -> plain functions that return rows and are asserted in tests with `expect`

## Common pitfalls

1. Do not open a new connection for every tiny assertion unless isolation requires it.
2. Do not convert every DAO class literally; collapse thin wrappers.
3. Do not hardcode credentials in test files.
4. Do not over-assert on every column when only a few business fields matter.
