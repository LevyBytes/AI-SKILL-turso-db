# Domain updates casts and table checks

[Context index](alter-analyze-attach-and-domain-reference.md) · [Setup and preceding context](domain-base-types-defaults-and-nullability.md) · [Previous phase](domain-checks-and-chaining.md) · [Continue](domain-operations-removal-and-restrictions.md)

### UPDATE Enforcement

 Domain constraints apply during UPDATE and INSERT.

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);

CREATE TABLE items (
    id INTEGER PRIMARY KEY,
    stock positive_int
) STRICT;

INSERT INTO items VALUES (1, 10);
UPDATE items SET stock = 20 WHERE id = 1;
SELECT stock FROM items;
-- 20

UPDATE items SET stock = -1 WHERE id = 1;
-- Error: domain positive_int constraint violation

```

### CAST Support

 CAST runs the domain's validation constraints:

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);

SELECT CAST(42 AS positive_int);
-- 42

SELECT CAST(-1 AS positive_int);
-- Error: domain positive_int constraint violation

```

 A CAST from NULL into a NOT NULL domain is rejected:

```
CREATE DOMAIN notnull_int AS integer NOT NULL;

SELECT CAST(NULL AS notnull_int);
-- Error: domain notnull_int does not allow null values

```

### CHECK Constraints with Table-Level Checks

 Both domain CHECK constraints and table-level CHECK constraints are applied. The domain constraint is evaluated during encoding, and the table CHECK is evaluated separately.

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);

CREATE TABLE bounded (
    id INTEGER PRIMARY KEY,
    val positive_int CHECK (val < 100)
) STRICT;

INSERT INTO bounded VALUES (1, 50);
-- OK: passes domain CHECK (> 0) and table CHECK (< 100)

INSERT INTO bounded VALUES (2, -1);
-- Error: fails domain CHECK

INSERT INTO bounded VALUES (3, 200);
-- Error: fails table CHECK

```
