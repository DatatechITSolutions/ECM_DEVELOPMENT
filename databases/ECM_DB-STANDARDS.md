# ECM Database Standards

**Parent:** [ECM Core Software Development Standards](../core/STANDARDS.md)  
**Applies to:** Oracle, DB2, and SQL Server schemas, queries, and database code that is new or materially modified

---

## Table of Contents

- [SQL Style and Naming](#sql-style-and-naming)
- [Parameterization and Dynamic SQL](#parameterization-and-dynamic-sql)
- [Indexing and Query Performance](#indexing-and-query-performance)
- [Transactions and Concurrency](#transactions-and-concurrency)
- [Oracle (PL/SQL) Best Practices](#oracle-plsql-best-practices)
- [DB2 Best Practices](#db2-best-practices)
- [SQL Server Best Practices](#sql-server-best-practices)
- [Database Security](#database-security)
- [SQL Pagination](#sql-pagination)

---

## SQL Style and Naming

- Use consistent naming: `snake_case` for columns, singular/plural standard for tables (pick one).
- Avoid reserved words and ambiguous abbreviations.
- Format SQL for readability: uppercase keywords; align joins; choose a consistent comma style.
- Avoid `SELECT *` in production code; select explicit columns.

### Example

✔ Good

```sql
SELECT
    u.user_id,
    u.username,
    u.created_at
FROM users u
WHERE u.user_id = :user_id;
```

Why:

- Explicit columns avoid accidental breaking changes and reduce IO.

❌ Avoid

```sql
SELECT * FROM users WHERE user_id = :user_id;
```

Why not:

- `SELECT *` causes fragile coupling and can load unnecessary data.

---

## Parameterization and Dynamic SQL

- Always use bind parameters (Oracle `:param`, SQL Server `@param`) / prepared statements.
- Avoid building SQL with string concatenation.
- If dynamic SQL is unavoidable (e.g., optional filters), use safe templating with strict allow-lists for identifiers.

---

## Indexing and Query Performance

- Index to support your `WHERE` + `JOIN` + `ORDER BY` patterns; avoid over-indexing (hurts writes).
- Use execution plans to confirm index usage; don’t guess.
- Avoid functions on indexed columns in predicates unless using function-based indexes (Oracle).
- Use pagination with stable ordering; avoid `OFFSET` without an index on the ordering column.

---

## Transactions and Concurrency

- Keep transactions short. Do not include user interaction in an open transaction.
- Use appropriate isolation levels; avoid unnecessary locks.
- In Oracle/DB2, understand MVCC/locking behavior; in SQL Server, be explicit when needed (e.g., snapshot options).

---

## Oracle (PL/SQL) Best Practices

- Prefer packages for grouping related procedures/functions; keep public APIs small.
- Use bulk operations (`BULK COLLECT` / `FORALL`) for large sets; avoid row-by-row loops.
- Use exceptions for exceptional cases, not control flow.
- Instrument: record module/action (`DBMS_APPLICATION_INFO`) and key parameters (excluding secrets).

### Bulk update example

```sql
-- Oracle: bulk update pattern
FORALL i IN 1..l_ids.COUNT
  UPDATE orders
     SET status = 'CLOSED'
   WHERE order_id = l_ids(i);
```

---

## DB2 Best Practices

- Use parameter markers and keep statements stable for better package cache reuse.
- Keep statistics up to date (`RUNSTATS`) and monitor reorg needs.
- Prefer set-based operations; avoid cursors unless necessary.
- Use appropriate isolation levels for OLTP workloads.

---

## SQL Server Best Practices

- Use stored procedures with parameters to reduce plan cache bloat and improve security.
- Beware of parameter sniffing; fix with hints only as a last resort (prefer better indexing or selective recompiles).
- Use `TRY...CATCH` with transaction-safe patterns; check `XACT_STATE()` on errors.
- Avoid scalar UDFs in hot paths; prefer inline TVFs or set-based logic.

---

## Database Security

- Use least-privilege roles; avoid sharing admin accounts.
- Encrypt sensitive data at rest (TDE where available) and in transit (TLS).
- Audit access to sensitive tables; log schema changes.
- Avoid storing secrets in tables; use vaults/secret managers.
- Enforce constraints (`NOT NULL`, FK, `CHECK`) as a last line of defense.

---

## SQL Pagination

```sql
-- Stable pagination with ordering (SQL Server / Oracle 12c+ style)
SELECT
    u.user_id,
    u.username
FROM users u
ORDER BY u.user_id
OFFSET :offset ROWS FETCH NEXT :page_size ROWS ONLY;
```
