# Detach database syntax and examples

[Context index](virtual-tables-delete-and-drop-reference.md)

## DETACH DATABASE

 DETACH DATABASE disconnects a database that was attached to the current connection. After DETACH DATABASE runs, tables in that database cannot be reached through its schema name.

### Syntax

```
DETACH [DATABASE] schema-name;

```

### Description

 DETACH closes the attached-database connection named by `schema-name` and releases associated resources. Later references to `schema-name.table-name` fail.

### Clauses

| Clause | Description |
| --- | --- |
| `DATABASE` | Optional keyword with no behavioral effect. |
| `schema-name` | Schema name given to the database at ATTACH time. |

 `main` and `temp` cannot be detached. DETACH is also invalid during an active transaction. Commit or roll back any open transaction before running DETACH.

### Examples

### Detach a Database

```
ATTACH 'archive.db' AS archive;

-- Use the attached database
SELECT count(*) FROM archive.events;

-- Detach when done
DETACH DATABASE archive;

```

### Detach Without the DATABASE Keyword

```
DETACH archive;

```

### See Also

- [ATTACH DATABASE](https://docs.turso.tech/sql-reference/statements/attach-database) when attaching another database
