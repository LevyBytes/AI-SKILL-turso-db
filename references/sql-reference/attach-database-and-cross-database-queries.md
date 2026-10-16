# Attach database and cross database queries

[Context index](alter-analyze-attach-and-domain-reference.md)

## ATTACH DATABASE

 The ATTACH DATABASE statement registers an additional database file on the current connection with a schema name. After attachment, tables from that external database can be queried together with tables from the main database.

### Syntax

```
ATTACH [DATABASE] filename AS schema-name;

```

### Description

 ATTACH opens the file identified by `filename` and exposes its tables under the supplied `schema-name`. Refer to tables in that database with `schema-name.table-name`.

### Clauses

| Clause | Description |
| --- | --- |
| `DATABASE` | Optional keyword; it does not change behavior. |
| `filename` | Provide a string expression resolving to the database file path to attach. Use `':memory:'` for an in-memory database. |
| `schema-name` | The qualifier name for table references from the attached database. It must be unique across every database attached to the connection. |

 Each connection includes a built-in schema named `main` for its primary database. `main` and `temp` are reserved, so attached databases cannot use them as schema names. Because this feature is experimental, it must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). Attached databases allow reads and writes, including INSERT, UPDATE, DELETE, CREATE TABLE, etc. The attached database's journal mode has to match the main database's journal mode, for example both WAL or both MVCC.

### Examples

### Attach and Query an External Database

```
-- Attach another database file
ATTACH DATABASE 'archive.db' AS archive;

-- Query a table in the attached database
SELECT * FROM archive.events WHERE event_date > '2025-01-01';

```

### Cross-Database Queries

```
ATTACH 'customers.db' AS customers_db;

-- Join tables across the main and attached databases
SELECT o.id, o.total, c.name
FROM main.orders o
JOIN customers_db.customers c ON o.customer_id = c.id;

```

### Attach an In-Memory Database

```
ATTACH ':memory:' AS scratch;

```

### See Also

- [DETACH DATABASE](https://docs.turso.tech/sql-reference/statements/detach-database) when removing an attached database
- [SELECT](https://docs.turso.tech/sql-reference/statements/select) when querying tables across databases
