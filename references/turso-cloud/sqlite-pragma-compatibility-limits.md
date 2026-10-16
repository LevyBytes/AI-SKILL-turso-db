# SQLite pragma compatibility limits

[Context index](access-durability-encryption-and-migration.md)

## Limitations

 Turso Cloud supports most SQLite features, with a few known limitations, especially for migrations from vanilla SQLite.

### Pragma statements

 Some SQLite pragma statements either work differently on Turso Cloud or are unsupported.

| Pragma | Notes |
| --- | --- |
| `user_version` | Read-only on Turso Cloud. Track schema migrations with a `_schema_version` table instead. |
| `application_id` | Read-only on Turso Cloud. Store application identifiers in a `_metadata` table instead. |
| `busy_timeout` | Not supported. Turso Cloud handles concurrency internally, so a busy timeout setting changes nothing. |
| `journal_mode` | Not supported. Turso Cloud handles journaling internally and prevents journal mode changes. |

 If `PRAGMA user_version` currently tracks schema migrations in vanilla SQLite, move to a table-based method on Turso Cloud:

```
CREATE TABLE IF NOT EXISTS _schema_version (version INTEGER NOT NULL);
INSERT INTO _schema_version (version) VALUES (0);

```
