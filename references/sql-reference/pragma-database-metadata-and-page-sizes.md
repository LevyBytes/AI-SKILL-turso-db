# Pragma database metadata and page sizes

[Context index](multiprocess-and-pragma-reference.md) · [Continue](pragma-schema-versions-and-table-inspection.md)

## PRAGMA Statements

 PRAGMA statements are SQLite/Turso-specific commands for reading or changing database configuration, retrieving metadata, and controlling database behavior; they are not standard SQL.

### Syntax

```
PRAGMA pragma-name;
PRAGMA pragma-name = value;
PRAGMA pragma-name(value);

```

### Database Metadata

### database_list

 Outputs a row for every attached database.

```
PRAGMA database_list;
-- seq | name | file
-- 0   | main | /path/to/database.db

```

### page_count

 Outputs the total page count for the database file.

```
PRAGMA page_count;
-- 42

```

### page_size

 Reads or changes the database page size; the value can be changed only before table creation begins.

```
PRAGMA page_size;
-- 4096

PRAGMA page_size = 8192;

```

### max_page_count

 Reads or changes the maximum page count permitted for the database file.

```
PRAGMA max_page_count;
PRAGMA max_page_count = 1000000;

```

### freelist_count

 Outputs how many unused pages remain in the database file.

```
PRAGMA freelist_count;

```

### encoding

 Outputs the database text encoding.

```
PRAGMA encoding;
-- UTF-8

```
