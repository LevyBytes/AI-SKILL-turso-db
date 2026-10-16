# Custom type inspection and alter table

[Context index](custom-types-and-view-reference.md) · [Setup and preceding context](custom-type-base-encoding-and-decoding.md) · [Previous phase](custom-type-defaults-validation-and-casts.md) · [Continue](struct-types-packing-and-extraction.md)

### PRAGMA list_types

 Shows every available type, covering both built-in and user-defined types:

```
PRAGMA list_types;
-- name
-- boolean
-- varchar
-- date
-- time
-- timestamp
-- smallint
-- numeric
-- ...

```

### sqlite_turso_types Virtual Table

 The `sqlite_turso_types` virtual table exposes each type's name and SQL definition:

```
SELECT name, sql FROM sqlite_turso_types;
-- name    | sql
-- boolean | CREATE TYPE boolean(value any) BASE integer ENCODE ...
-- ...

```

### Using with ALTER TABLE

 Custom types are valid in ALTER TABLE ADD COLUMN when adding new columns:

```
ALTER TABLE users ADD COLUMN email varchar(255);

```

 The new column uses the same STRICT type-checking behavior as columns declared in the original CREATE TABLE.

### Built-in Types

 Turso includes these built-in custom types for use in STRICT tables:

| Type | Base | Parameters | Description |
| --- | --- | --- | --- |
| `boolean` | integer | — | Constrained to 0 or 1. Aliases: `bool`. |
| `smallint` | integer | — | Integer constrained to -32768 through 32767. Aliases: `int2`. |
| `bigint` | integer | — | Integer (no range constraint). Aliases: `int8`. |
| `varchar(N)` | text | `maxlen` | Text capped at N characters. |
| `date` | text | — | ISO 8601 date (YYYY-MM-DD), checked during insert. |
| `time` | text | — | ISO 8601 time (HH:MM:SS), checked during insert. |
| `timestamp` | text | — | ISO 8601 datetime, validated on insert. |
| `numeric(P,S)` | blob | `precision`, `scale` | Fixed-point decimal with P digits total and S digits after the decimal point. Arithmetic operators are supported. |
| `uuid` | blob | — | UUID stored as a 16-byte blob and shown as a string. Default: `uuid4_str()`. |
| `inet` | text | — | Validated IP address, either IPv4 or IPv6. |
| `bytea` | blob | — | Binary data (PostgreSQL-compatible alias). |
| `json` | text | — | Validated JSON text. |
| `jsonb` | blob | — | JSON kept in binary form and returned as JSON text when read. |

 The `uuid`, `json`, and `jsonb` types depend on their matching extensions being compiled in. Standard Turso builds include them by default.

### Composite Types: STRUCT and UNION

 CREATE TYPE supports two composite forms in addition to encode/decode custom types: **STRUCT** (a named product type) and **UNION** (a discriminated union / tagged variant). Both require STRICT tables and store their data on disk as blobs.
