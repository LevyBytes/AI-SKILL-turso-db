# Custom type base encoding and decoding

[Context index](custom-types-and-view-reference.md) · [Continue](custom-type-parameters-and-operators.md)

## CREATE TYPE

 **Turso Extension**: CREATE TYPE is specific to Turso and is not part of standard SQLite. It can be used only with STRICT tables, and the experimental feature has to be turned on first; see [experimental features](https://docs.turso.tech/sql-reference/experimental-features). A CREATE TYPE definition creates a user-defined type that determines storage encoding, read-time decoding, input validation, sort comparison behavior, and the default value. These custom types expand the STRICT table type system past SQLite's five built-in storage classes.

### Syntax

```
CREATE TYPE [IF NOT EXISTS] type-name [(parameters)]
    BASE base-type
    ENCODE encode-expr
    DECODE decode-expr
    [OPERATOR 'op' [function-name] ...]
    [DEFAULT default-expr];

```

### Description

 A custom type attaches user-defined behavior to one of the four base storage types. On writes to a column using that type, ENCODE converts the value into its stored representation. On reads, DECODE converts the stored value back. This makes it possible to keep compact data on disk while exposing another shape to queries. Custom types apply only in STRICT tables; in a non-STRICT table, a custom type name changes nothing.

### Clauses

### IF NOT EXISTS

 Avoids raising an error when a type with that name is already present. The existing type definition is not modified.

```
CREATE TYPE IF NOT EXISTS cents BASE integer ENCODE value * 100 DECODE value / 100;

```

### BASE

 Chooses the SQLite storage class used as the on-disk representation. A BASE clause is required for every custom type.

| Base Type | Description |
| --- | --- |
| `integer` | Signed integer using 1-8 bytes, with size chosen by magnitude |
| `real` | 8-byte IEEE 754 floating-point number |
| `text` | UTF-8 encoded string |
| `blob` | Raw binary data |

```
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100;

```

### ENCODE / DECODE

 ENCODE runs each time a value is written into a column of this type, while DECODE runs each time the value is read back. In both expressions, the identifier `value` denotes the input.
- **ENCODE**: Converts the incoming value to the base storage representation. Executes for INSERT, UPDATE, and CAST.
- **DECODE**: Converts the stored representation back to the presented value. Executes for SELECT.
- **NULL handling**: NULL skips ENCODE and DECODE entirely. Given NULL input, the result is NULL and the expression is not evaluated.

```
-- Store text in reversed form
CREATE TYPE reversed_text BASE text
    ENCODE turso_reverse(value)
    DECODE turso_reverse(value);

```

 Put validation rules in ENCODE. When ENCODE raises an error, the associated INSERT or UPDATE does not continue.
