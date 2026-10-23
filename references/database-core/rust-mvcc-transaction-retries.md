# Rust MVCC transaction retries

[Context index](embedded-database-mvcc-and-encryption.md) · [Setup and preceding context](mvcc-setup-and-conflict-handling.md) · [Continue](javascript-mvcc-transaction-retries.md)

Use the Turso Rust binding with `tokio` (macros and runtime), `rand` 0.9 and `tempfile` 3. Each writer uses its own connection. The retry budget is 100 attempts; an insert failure never proceeds to commit.

The complete program is in the [upstream source](https://raw.githubusercontent.com/tursodatabase/turso/0687b18d5275ff3c8383b48479394e65e8cc0ec2/bindings/rust/examples/concurrent_writes.rs). Correct these defects when adapting it:

- The source chains `.and(conn.execute("COMMIT", ()).await)` onto the awaited `INSERT`. Its argument is awaited before `and` runs, so `COMMIT` executes even when the `INSERT` failed. Run `BEGIN CONCURRENT`, `INSERT` and `COMMIT` in sequence inside one `async` block with `?`, and match on that block's result.
- Keep `BEGIN CONCURRENT` inside the retried block. The source's `?` on `BEGIN` ends the task on a busy error without a rollback or a retry.
- Bind the value as a parameter (`"INSERT INTO hits VALUES (?)", [val]`) instead of formatting it into the SQL.
- Bound the retry loop, about 100 attempts, and await every task before returning the first error. The source's `?` on the first failed handle returns while other tasks still run.
