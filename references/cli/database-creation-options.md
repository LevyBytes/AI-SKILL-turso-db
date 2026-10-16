# Database creation options

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](show-database-access-rules.md) · [Continue](database-creation-source-examples.md)

## `turso db create`

Use this command to create a database inside a chosen group:

```
turso db create [database-name]

```

 `[database-name]` is optional. When it is omitted, Turso uses your project name. If multiple groups exist, include the `--group` flag.

### Flags

| Flag | Description |
| --- | --- |
| `--enable-extensions` | Turns on experimental SQLite extensions support. |
| `--from-csv` | Build the database from a csv file. |
| `--csv-table-name` | Table name inside the csv file. |
| `--from-db` | Choose a different database as the data source. |
| `--timestamp` | Choose an earlier point in time for copying data from the selected database. Use only with the `--from-db` flag. The value must use RFC3339 format, for example `2023-09-29T10:16:13-03:00` |
| `--from-dump` | Build the database from a local SQLite dump. |
| `--from-dump-url` | Build the database from a remote SQLite dump. |
| `--from-file` | Build the database from a local SQLite3-compatible file. |
| `--group` | Place the database in the chosen group. |
| `--size-limit` | Maximum database size in bytes. Unit-suffixed values are accepted too, such as 1mb, 256mb, 1gb. |
| `--canary` | Use database canary build. |
| `-w`, `--wait` | Wait until the database can accept requests. |
| `-h`, `--help` | Get help for create. |

### Examples

 The following examples show common ways to use the `db create` command.
