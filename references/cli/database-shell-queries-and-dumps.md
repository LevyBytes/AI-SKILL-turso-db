# Database shell queries and dumps

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](database-usage-inventory-and-locations.md) · [Continue](database-details-and-connection-urls.md)

## `turso db shell`

Use this command to connect directly to a Turso database:

```
turso db shell <database-name> [sql] [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `--instance <instance>` | Connect to the chosen database instance. |
| `--location <location-code>` | Connect to the database in the chosen location. |
| `--proxy <url>` | Proxy used for the database connection. |

### Examples

### Execute SQL

 Run SQL against a database directly from the shell:

```
turso db shell <database-name> "SELECT * FROM users"

```

### Database dump

 Use this command to dump the contents of a Turso database:

```
turso db shell <database-name> .dump > dump.sql

```

 A `.dump` output can recreate a database and excludes libSQL or SQLite internal tables.

### Load from dump

 Use this command to load a dump file into a new database:

```
turso db shell <database-name> < dump.sql

```

### Shell with libSQL server

 When running `turso dev` locally, provide your database URL to open the shell:

```
turso db shell http://127.0.0.1:8080

```
