# Point in time database recovery

[Context index](replica-hosting-and-recovery-workflows.md)

## How it works

Turso provides point-in-time recovery (PITR) for databases, so a database can be restored to a chosen point in time. It is useful after user mistakes such as accidentally dropping a table. Backups are generated automatically at `COMMIT`. Free plan users can restore anywhere within the last 24 hours. Developer, Scaler, and Pro plan restore windows cover the last 10, 30, or 90 days, respectively.

### How it works

1. Create a new database from the existing database through the CLI or API.
2. Update the application so it uses the new database connection string.
3. Delete the old database once it is no longer needed.
 The newly created database contains only data from before the requested timestamp. There can also be as much as a 15 seconds gap in the data immediately before that timestamp, depending on when Turso last completed its periodic batch checkpoint.

### Usage

```
turso db create my-new-database --from-db my-existing-database --timestamp 2024-01-01T00:00:00Z

```

 For details about every argument, use these references:

- [CLI timestamp flag](https://docs.turso.tech/cli/db/create)
- [Platform API recovery seed](https://docs.turso.tech/api-reference/databases/create)

### Things to know

- A PITR restore creates a new database, so the application must be updated to use the new database connection string.
- A PITR restore cannot target an existing database.
- To connect to the new database, [create a new token](https://docs.turso.tech/cli/db/tokens/create) or use a group token.
- Delete the old database manually after it is no longer needed.
- Restores are counted against the database quota for your plan.
