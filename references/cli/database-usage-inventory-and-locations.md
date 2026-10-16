# Database usage inventory and locations

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](database-destruction-export-and-import.md) · [Continue](database-shell-queries-and-dumps.md)

## `turso db inspect`

Use this command to inspect database usage, including storage consumed plus rows read and written:

```
turso db inspect <database-name>

```

### Flags

| Flag | Description |
| --- | --- |
| `--queries` | Show database query statistics. |
| `--verbose` | Include extra details such as location and instance type. |

### Examples

### Top Queries

 Use the Turso CLI to retrieve the SQL statements run most often or consuming the most resources against your database:

```
turso db inspect <database-name> --queries

```

## `turso db list`

Run this command to list every database available to the current user or organization:

```
turso db list

```

### Flags

| Flag | Description |
| --- | --- |
| `--group <group-name>` | Filter databases by group. |

## `turso db locations`

Run this command to list the supported database locations:

```
turso db locations

```

### Flags

| Flag | Description |
| --- | --- |
| `-l`, `--show-latencies` | Show latency from your current location to each Turso location. |
