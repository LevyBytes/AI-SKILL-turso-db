# Database creation source examples

[Context index](database-and-group-commands.md) · [Setup and preceding context](api-token-list-and-scopes.md) · [Previous phase](database-creation-options.md) · [Continue](database-destruction-export-and-import.md)

### Create database in a specific group

 Create a database in a chosen group by passing `--group`:

```
turso db create [database-name] --group <group-name>

```

### Create database from SQLite file

 Create a Turso database from an existing SQLite file with `--from-file`:

```
turso db create [database-name] --from-file ./path/to/file.db

```

 Files can be no larger than 2GB.

### Create database from SQLite dump

 Create a database from `.dump` output with `--from-dump`:

```
turso db create [database-name] --from-dump ./path/to/dump.sql

```

### Create database from another existing database

 Create a Turso database by copying an existing database with `--from-db`:

```
turso db create [database-name] --from-db <existing-database-name>

```

### Create database from a point in time

 Create a Turso database from an existing database at a chosen time by using `--from-db` with `--timestamp`:

```
turso db create [database-name] --from-db <existing-database-name> --timestamp 2024-01-01T10:10:10-10:00

```

### Create database from a CSV file

 Create a Turso database from a CSV file with `--from-csv`:

```
turso db create [database-name] --from-csv ./path/to/file.csv --csv-table-name <desired-table-name>

```

### Create a database with extensions enabled

 Create a database with experimental SQLite [extensions](https://docs.turso.tech/libsql#extensions) support by passing `--enable-extensions`:

```
turso db create [database-name] --enable-extensions

```

 Passing `--enable-extensions` turns on extensions for every database in the group.
