# Help, Turso Cloud

> Turso Cloud reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Rows Read; Vector Search.

## Rows Read

On Starter and Scaler plans, Turso applies monthly limits using the usage measurements collected within each calendar month:
- Number of table rows read
- Number of table rows written
- Amount of total storage
 For plans with monthly quotas on row reads, row writes, and total storage, a query that goes beyond those quotas fails and reports the `BLOCKED` error code.

### Rows Read

 Within SQLite, "row read" is best understood as a "row scan" performed while a statement runs. Keep these Turso CLI metric details in mind:
- **SQL Queries:** The number of rows scanned can be higher than the result count.
- **SQL Updates:** Updating a row requires at least one row scan for that row.

### Aggregate Function Impact

 Aggregate functions such as `count`, `avg`, `min`, `max`, and `sum` scan each row included in the aggregate calculation. For better efficiency, keep aggregate results in a separate table and update that table transactionally alongside changes to the underlying data.

### Full Table Scans

 When a query cannot use an index, SQLite scans the entire table and counts one row scan for every row in it. Use query and schema designs that avoid expensive table-wide scans where possible.

### Complex Query Costs

 Joins, subqueries, and compound queries add row-scan cost for every row considered across each table participating in the operation.

### Update Mechanics

 A SQL update reads and writes the rows it changes. If there is no usable index for the filter condition, SQLite scans the full table, counting a read for every table row and a write for every row actually updated.

### `ALTER TABLE` and Row Reads

 Some `ALTER TABLE` operations, particularly ones that rewrite row data, require scanning the full table and therefore count a read for each row. Other `ALTER TABLE` forms, including `DROP COLUMN`, do not always force full scans. Row writes may also be part of the cost.

### Indexing Costs

 Creating an index on a table that already has data causes SQLite to scan the table, producing one read for each existing row.

### SQLite System Tables

 Queries against SQLite internal tables, including `dbstat` and tables whose names begin with the `sqlite_` prefix, do not count as row reads.

### Zero-Read Commands

 Statements that do not actually read or write table rows, such as `select 1`, are counted as one row read by default.

### Rows Written

 In SQLite, "row written" covers both adding a new row and changing an existing row.

### `ALTER TABLE` and Row Writes

 An `ALTER TABLE` statement may count a row write for every row already in the table, especially when the operation changes stored row data. Different `ALTER TABLE` forms have different effects on row-write accounting.

### Implications of Aborted Transactions

 Rows inserted or updated inside a transaction still count as row writes even when the transaction is later aborted. Managing transactions carefully can help control write usage. `ALTER TABLE` operations can also introduce row reads, so schema changes need separate cost consideration.

### Total Storage

 SQLite uses the virtual table [`dbstat`](https://www.sqlite.org/dbstat.html) to determine total space consumed by tables and indexes. Storage is measured in database file pages, and each page is 4KB. The `VACUUM` command commonly compacts SQLite databases to reduce storage use, but Turso currently disables it. Future Turso changes may add developer-facing ways to manage and shrink total database storage more directly.

### Reducing Usage

### Query Execution

 Learning how the [SQLite query planner](https://www.sqlite.org/queryplanner.html) works helps clarify the way SQLite executes queries and where optimization opportunities exist.

### Query Planning

 Use [`EXPLAIN QUERY PLAN`](https://www.sqlite.org/eqp.html) to inspect the execution plan for a query. It can show whether SQLite is scanning a full table and whether the chosen index is effective for reducing avoidable reads.

### Indexing

 Design queries so row filtering can use appropriate indexes. Without a suitable index, SQLite falls back to scanning the full table, adding one read for each row. Good indexing is therefore central to keeping read overhead low. Prefer creating required indexes when the table is created. If an index is added after rows already exist, SQLite scans the table and counts one read per existing row. Active index planning helps preserve database performance.

## Vector Search

Turso Cloud currently uses **libSQL** (SQLite-compatible). A next-generation Turso Database engine is in alpha development, with future Turso Cloud integration planned. Turso Cloud provides a managed SQLite-compatible database platform for workloads ranging from prototypes through production. Built on [libSQL](https://docs.turso.tech/libsql), it combines SQLite-style performance and reliability with managed cloud database scalability and convenience.

### Vector Search

Built-in similarity search supports AI apps and RAG workflows without requiring extensions.

### Replication & Sync

Use Turso Cloud to synchronize devices with one another whenever needed.

### Branching

Create fast isolated Copy-on-Write branches.

### Analytics

Track database usage and performance across your databases.

### Bring-Your-Own-Key Encryption

Protect sensitive data with customer-controlled keys. Each database uses its own key for complete isolation.

### Scoped Access Tokens

Use fine-grained tokens scoped to database, table, action, and expiration. Permissions can be read-only, write, or custom.

### Team Access

Work with team members on databases and control their access permissions.

### Fully Managed

Turso manages infrastructure, scaling, and security for you.

### Backups and Recovery

Recover your database from any point in time.

### Start building

 Use the CLI, API and SDKs to manage, distribute and connect your databases.

### Turso CLI

Use the Turso CLI to administer groups, databases, and API tokens.

### Turso Platform API

Use the Platform API to manage groups and databases from code.

### Client SDKs

Connect your application to Turso with one of the available libSQL drivers.

### Tutorials

Find guides for using Turso with preferred languages and frameworks.

### Community

 Participate in the Turso community to ask questions, show what you are building, discuss best practices, and exchange tips on [Discord](https://tur.so/discord), [Twitter](https://twitter.com/tursodatabase), and [LinkedIn](https://www.linkedin.com/company/turso).

### Discord

### GitHub

### X (Twitter)
