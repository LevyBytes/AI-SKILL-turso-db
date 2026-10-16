# Storage classes affinity and conversions

[Context index](data-types-and-experimental-features.md) · [Continue](strict-tables-and-custom-types.md)

## Data Types

 Turso follows SQLite's dynamic typing model: the type is attached to each stored value, while ordinary columns do not require one fixed type unless the table is `STRICT`. Stored values fall into five storage classes. For `STRICT` tables, Turso adds custom types, composite types, and native array types on top of that model.

### Storage Classes

| Storage Class | Description |
| --- | --- |
| NULL | The NULL value |
| INTEGER | Signed integer storage uses 1, 2, 3, 4, 6, or 8 bytes, selected according to the value's magnitude |
| REAL | IEEE 754 floating-point value using 8 bytes |
| TEXT | A UTF-8 encoded string |
| BLOB | Binary bytes kept unchanged from the provided input |

```
SELECT typeof(NULL);       -- 'null'
SELECT typeof(42);         -- 'integer'
SELECT typeof(3.14);       -- 'real'
SELECT typeof('hello');    -- 'text'
SELECT typeof(x'CAFE');    -- 'blob'

```

### Type Affinity

 Declaring a type name on a column gives that column a **type affinity** in Turso. This affinity guides storage conversion choices rather than acting as a hard rule, except in `STRICT` tables. The affinity rules match SQLite.

### Affinity Determination Rules

 Turso derives a column's type affinity from its declared type name by checking the following rules sequentially:

| Rule | Condition | Affinity | Examples |
| --- | --- | --- | --- |
| 1 | Type name contains “INT” | INTEGER | `INT`, `INTEGER`, `BIGINT`, `SMALLINT`, `TINYINT` |
| 2 | Type name includes "CHAR", "CLOB", or "TEXT" | TEXT | `TEXT`, `VARCHAR(255)`, `CLOB`, `CHARACTER(20)` |
| 3 | Type name includes "BLOB", or the column has no declared type | BLOB | `BLOB`, (no type) |
| 4 | Type name includes "REAL", "FLOA", or "DOUB" | REAL | `REAL`, `FLOAT`, `DOUBLE`, `DOUBLE PRECISION` |
| 5 | Otherwise | NUMERIC | `NUMERIC`, `DECIMAL`, `BOOLEAN`, `DATE` |

```
-- Type affinity is a suggestion, not a constraint
CREATE TABLE flexible (
    id INTEGER,
    name TEXT,
    data BLOB
);

-- This works - TEXT value in an INTEGER column
INSERT INTO flexible VALUES ('not a number', 42, 'text in blob');
SELECT typeof(id), typeof(name), typeof(data) FROM flexible;
-- 'text', 'text', 'text'

```

### Type Conversions

 On insert, Turso may coerce the incoming value according to the target column's affinity:
- **INTEGER affinity**: TEXT or REAL input that has integer form is stored by Turso as INTEGER
- **REAL affinity**: TEXT input with numeric form is changed to REAL; integer input is also changed to REAL by Turso
- **NUMERIC affinity**: Turso attempts INTEGER conversion, then REAL conversion, and otherwise leaves the value as TEXT
- **TEXT affinity**: INTEGER and REAL inputs are rendered into text form
- **BLOB affinity**: Turso leaves the value unconverted
