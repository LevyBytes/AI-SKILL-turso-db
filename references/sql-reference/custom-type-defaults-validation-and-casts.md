# Custom type defaults validation and casts

[Context index](custom-types-and-view-reference.md) · [Setup and preceding context](custom-type-base-encoding-and-decoding.md) · [Previous phase](custom-type-parameters-and-operators.md) · [Continue](custom-type-inspection-and-alter-table.md)

### DEFAULT

 DEFAULT defines the default at the type level. If a column of this type has no DEFAULT of its own, this value is applied.

```
CREATE TYPE boolean BASE integer
    ENCODE boolean_to_int(value)
    DECODE CASE WHEN value THEN 1 ELSE 0 END
    OPERATOR '<'
    DEFAULT 0;

```

 A DEFAULT specified on the column takes precedence over the type-level DEFAULT:

```
CREATE TABLE flags (
    id INTEGER PRIMARY KEY,
    is_active boolean,              -- uses type default (0)
    is_admin boolean DEFAULT 1      -- overrides type default
) STRICT;

```

### Validation with RAISE

 Validate writes by using CASE/WHEN together with RAISE inside ENCODE. If the check is not satisfied, the statement stops with the configured error message.

```
CREATE TYPE positive_int BASE integer
    ENCODE CASE
        WHEN value > 0 THEN value
        ELSE RAISE(ABORT, 'value must be positive')
    END
    DECODE value;

```

```
CREATE TYPE email BASE text
    ENCODE CASE
        WHEN value LIKE '%_@_%.__%' THEN lower(value)
        ELSE RAISE(ABORT, 'invalid email address')
    END
    DECODE value;

```

### CAST Support

 The CAST expression runs the custom type's ENCODE behavior:

```
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100;

SELECT CAST(42.5 AS cents);
-- 4250

```

 Use this when a value needs to be converted to its encoded representation outside INSERT/UPDATE, including in WHERE clauses and CHECK constraints.

### CHECK Constraints with Custom Types

 For STRICT tables that use custom types, CHECK constraints evaluate the **decoded** presentation values. To compare a custom-typed column to a literal, encode the literal with CAST:

```
CREATE TYPE cents BASE integer
    ENCODE value * 100
    DECODE value / 100;

CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    price cents CHECK(price >= CAST(0 AS cents))
) STRICT;

```

### Inspecting Types
