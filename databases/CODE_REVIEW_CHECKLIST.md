## Database Code Review Checklist

- [ ] The change implements the ticket scope and meets every acceptance criterion. Schema, query, and data-path edge cases are handled. - P0
- [ ] Naming is consistent: `snake_case` for columns, one agreed singular/plural standard for tables, and no reserved words or ambiguous abbreviations. - P0
- [ ] SQL is formatted for readability: uppercase keywords, aligned joins, and a consistent comma style. - P0
- [ ] Production SQL selects explicit columns. There is no `SELECT *`. - P0
- [ ] All statements use bind parameters (Oracle `:param`, SQL Server `@param`) or prepared statements. SQL is not built with string concatenation. - P0
- [ ] If dynamic SQL is unavoidable, identifiers come from a strict allow-list. User input is never interpolated into SQL text. - P0
- [ ] Indexes support the actual `WHERE`, `JOIN`, and `ORDER BY` patterns. Writes are not hurt by unnecessary extra indexes. - P0
- [ ] Execution plans confirm that the intended indexes are used. Predicates do not wrap indexed columns in functions unless a function-based index exists (Oracle). - P0
- [ ] List queries use stable pagination and ordering. `OFFSET` is not used without an index on the ordering column. - P0
- [ ] Transactions are short, have an appropriate isolation level, and do not stay open across user interaction. Unnecessary locks are avoided. - P0
- [ ] Oracle / PL/SQL changes group related routines in packages with a small public API, use `BULK COLLECT` / `FORALL` for large sets, and do not use exceptions for ordinary control flow. - P0
- [ ] DB2 changes keep statements stable with parameter markers for package-cache reuse, prefer set-based SQL over cursors, and note `RUNSTATS` / reorg impact where statistics or physical layout change. - P0
- [ ] SQL Server changes use parameterized stored procedures, avoid scalar UDFs on hot paths, treat parameter-sniffing hints as a last resort, and use `TRY...CATCH` with `XACT_STATE()` for transaction-safe errors. - P0
- [ ] Security is preserved: least-privilege roles, no shared admin accounts, no secrets stored in tables, constraints (`NOT NULL`, FK, `CHECK`) enforced, and sensitive access or schema changes audited. Encryption at rest (TDE where available) and in transit (TLS) is not weakened. - P0
- [ ] Instrumentation does not log secrets. Oracle changes record module/action (`DBMS_APPLICATION_INFO`) and key parameters where useful. The change stays set-based unless a cursor or row-by-row loop is justified. - P0
