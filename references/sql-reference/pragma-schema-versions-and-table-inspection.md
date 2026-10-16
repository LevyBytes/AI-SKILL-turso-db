# Pragma schema versions and table inspection

[Context index](multiprocess-and-pragma-reference.md) · [Setup and preceding context](pragma-database-metadata-and-page-sizes.md) · [Continue](pragma-index-functions-and-journal-mode.md)

### schema_version

 Outputs the schema version number, which increases whenever the schema changes.

```
PRAGMA schema_version;
-- 5

```

### application_id

 Reads or changes the application ID stored in the database header. Applications may use this 32-bit integer to identify the database file format.

```
PRAGMA application_id;
PRAGMA application_id = 12345;

```

### user_version

 Reads or changes the user version number, a 32-bit integer reserved for application use.

```
PRAGMA user_version;
PRAGMA user_version = 3;

```

### Schema Introspection

### table_info

 Outputs a row for every column in the named table.

```
PRAGMA table_info(table-name);

```

| Column | Type | Description |
| --- | --- | --- |
| cid | INTEGER | Column index (0-based) |
| name | TEXT | Column name |
| type | TEXT | Declared type name |
| notnull | INTEGER | 1 if NOT NULL constraint exists |
| dflt_value | TEXT | Default value expression, or NULL |
| pk | INTEGER | 1 when the column belongs to the PRIMARY KEY |

```
CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT NOT NULL, email TEXT);
PRAGMA table_info(users);
-- cid | name  | type    | notnull | dflt_value | pk
-- 0   | id    | INTEGER | 0       |            | 1
-- 1   | name  | TEXT    | 1       |            | 0
-- 2   | email | TEXT    | 0       |            | 0

```

### table_xinfo

 Like `table_info`, but it also reports hidden columns and extra metadata.

```
PRAGMA table_xinfo(table-name);

```

 Outputs the `table_info` columns plus `hidden`, where 0 indicates a normal column and non-zero identifies hidden columns in virtual tables.

### table_list

 Outputs a row for every table and view in the database.

```
PRAGMA table_list;
-- schema | name    | type  | ncol | wr | strict
-- main   | users   | table | 3    | 0  | 0
-- main   | orders  | table | 4    | 0  | 0

```
