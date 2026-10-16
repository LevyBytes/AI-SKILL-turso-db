# Database destruction export and import

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](database-creation-source-examples.md) · [Continue](database-usage-inventory-and-locations.md)

## `turso db destroy`

Use this command to destroy a database:

```
turso db destroy <database-name> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `-y`, `--yes` | Confirms the destruction the database. |

## `turso db export`

Export a Turso database snapshot into a SQLite file. The command writes the current generation of a Turso database to a local SQLite file, but that file might not include the newest changes. After export, use SDK sync to bring the database up to date.

```
turso db export <database> [flags]

```

### Flags

| Flag | Description |
| --- | --- |
| `--output-file` | Set the output filename (default: `<database>.db`). |
| `--overwrite` | Replace the output file when it already exists. |
| `--with-metadata` | Include metadata in the export |
| `-h`, `--help` | Help for export |

## `turso db import`

Use this command to import an existing SQLite file into Turso Cloud:

```
turso db import ~/path/to/database.db

```

 Confirm that the SQLite file uses WAL journal mode. If your account has multiple groups, include `--group`.

### Flags

| Flag | Description |
| --- | --- |
| `--group` | Import the database into the selected group. |
| `-h`, `--help` | Get help for import. |
