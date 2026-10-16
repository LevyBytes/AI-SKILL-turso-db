# Domain checks and chaining

[Context index](alter-analyze-attach-and-domain-reference.md) · [Setup and preceding context](domain-base-types-defaults-and-nullability.md) · [Continue](domain-updates-casts-and-table-checks.md)

### CHECK

 Adds validation. The expression may use `value` for the input value under test. You can specify multiple CHECK constraints, and every one must pass.

```
CREATE DOMAIN positive_int AS integer CHECK (value > 0);

CREATE TABLE measurements (
    id INTEGER PRIMARY KEY,
    reading positive_int
) STRICT;

INSERT INTO measurements VALUES (1, 42);
-- OK

INSERT INTO measurements VALUES (2, -5);
-- Error: domain positive_int constraint violation

```

### CONSTRAINT name CHECK

 A CHECK constraint may be named when you want documentation-only labeling.

```
CREATE DOMAIN valid_score AS integer
    CONSTRAINT non_negative CHECK (value >= 0)
    CONSTRAINT max_hundred CHECK (value <= 100);

```

### Domain Chaining

 One domain may take another domain as its base type. In a chain, constraints are enforced across every level, from child through ancestor.

```
CREATE DOMAIN base_amount AS integer CHECK (value > 0);
CREATE DOMAIN small_amount AS base_amount CHECK (value < 1000);

CREATE TABLE orders (
    id INTEGER PRIMARY KEY,
    quantity small_amount
) STRICT;

INSERT INTO orders VALUES (1, 50);
-- OK: passes both value > 0 and value < 1000

INSERT INTO orders VALUES (2, -1);
-- Error: fails base_amount's CHECK (value > 0)

INSERT INTO orders VALUES (3, 5000);
-- Error: fails small_amount's CHECK (value < 1000)

```

 Chaining can include three or more levels:

```
CREATE DOMAIN text_val AS text;
CREATE DOMAIN nonempty AS text_val CHECK (length(value) > 0);
CREATE DOMAIN short_text AS nonempty CHECK (length(value) < 50);

CREATE TABLE labels (
    id INTEGER PRIMARY KEY,
    name short_text
) STRICT;

INSERT INTO labels VALUES (1, 'OK');
-- OK

INSERT INTO labels VALUES (2, '');
-- Error: fails nonempty's CHECK (length(value) > 0)

```
