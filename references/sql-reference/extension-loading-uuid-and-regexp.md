# Extension loading uuid and regexp

[Context index](expressions-and-extensions-workflows.md) · [Continue](extension-regexp-and-vector-examples.md)

## Extensions

 Turso includes multiple built-in extensions for specialized SQL functions and virtual tables. You can load extensions at runtime with the `load_extension()` function.

### Loading Extensions

```
SELECT load_extension('extension_name');

```

 Turso can load Turso-native extensions. Loadable SQLite `.so`/`.dll` extensions are unsupported.

### UUID Extension

 The UUID extension includes functions to create UUIDs and operate on them. By default, UUID values are stored as 16-byte BLOBs.

### Functions

| Function | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| `uuid4()` | none | BLOB | Generate a random UUID v4 |
| `uuid4_str()` | none | TEXT | Create a random UUID v4 string. Alias: `gen_random_uuid()` |
| `uuid7()` | none | BLOB | Create a time-ordered UUID v7 |
| `uuid7(seconds)` | INTEGER | BLOB | Create a UUID v7 using supplied seconds since epoch |
| `uuid7_timestamp_ms(uuid)` | BLOB | INTEGER | Read milliseconds since epoch from a UUID v7 |
| `uuid_str(uuid)` | BLOB | TEXT | Change a UUID blob into its string representation |
| `uuid_blob(uuid)` | TEXT | BLOB | Change a UUID string into its blob representation |

### Examples

```
-- Generate UUIDs
SELECT uuid4_str();
-- '550e8400-e29b-41d4-a716-446655440000'

SELECT uuid_str(uuid4());
-- 'f47ac10b-58cc-4372-a567-0e02b2c3d479'

-- UUID v7 is time-ordered (good for primary keys)
SELECT uuid_str(uuid7());
-- '0190a5c0-1234-7abc-8def-0123456789ab'

-- Extract timestamp from UUID v7
SELECT uuid7_timestamp_ms(uuid7());
-- 1720000000000

-- Use in a table
CREATE TABLE documents (
    id BLOB PRIMARY KEY DEFAULT (uuid7()),
    title TEXT
);
INSERT INTO documents (title) VALUES ('My Document');
SELECT uuid_str(id), title FROM documents;

```

### Regexp Extension

 The regexp extension supplies regular expression functions compatible with [sqlean-regexp](https://github.com/nalgeon/sqlean/blob/main/docs/regexp.md).

### Functions

| Function | Parameters | Return Type | Description |
| --- | --- | --- | --- |
| `regexp(pattern, source)` | TEXT, TEXT | INTEGER | Returns 1 if source matches pattern |
| `regexp_like(source, pattern)` | TEXT, TEXT | INTEGER | Returns 1 when source matches pattern, with the argument order reversed |
| `regexp_substr(source, pattern)` | TEXT, TEXT | TEXT | Returns the first substring that matches pattern, or NULL |
| `regexp_capture(source, pattern)` | TEXT, TEXT | TEXT | Returns the match for the first capture group |
| `regexp_capture(source, pattern, n)` | TEXT, TEXT, INTEGER | TEXT | Returns the match for the n-th capture group |
| `regexp_replace(source, pattern, replacement)` | TEXT, TEXT, TEXT | TEXT | Replace matches with replacement string |
