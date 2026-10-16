# MVCC setup and conflict handling

[Context index](embedded-database-mvcc-and-encryption.md) · [Continue](rust-mvcc-transaction-retries.md)

## Enable MVCC

With the standard Turso settings, write access is limited to a single connection at once. Enabling MVCC (Multi-Version Concurrency Control) lets several connections perform writes at the same time. When two transactions change identical data, one transaction gets a conflict error and needs to roll back before retrying.

### Enable MVCC

 Configure the journal mode through your database connection:

```
PRAGMA journal_mode = 'mvcc';

```

### BEGIN CONCURRENT

 Start a transaction with `BEGIN CONCURRENT` rather than `BEGIN` when you want other writers to continue running concurrently:

```
BEGIN CONCURRENT;
-- your writes here
COMMIT;

```

 When two transactions affect identical rows, one gets a conflict error and needs to roll back before trying again. Writes against separate data can continue without conflicts.

### Handling Conflicts

 Your application needs to identify conflict errors and rerun the transaction.
