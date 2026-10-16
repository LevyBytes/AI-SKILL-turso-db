# Upsert conflict targets and excluded values

[Context index](update-upsert-and-vacuum-reference.md) · [Continue](upsert-conditions-counters-and-bulk-inserts.md)

## INSERT … ON CONFLICT (Upsert)

 The upsert clause extends `INSERT` by defining behavior for inserts that would break a `UNIQUE` or `PRIMARY KEY` constraint. Rather than returning an error, the statement may skip the conflicting row or update the existing one.

### Syntax

```
INSERT INTO table-name [(column-name [, column-name ...])]
VALUES (expression [, expression ...]) [, ...]
ON CONFLICT [(conflict-target)] DO NOTHING
[ON CONFLICT [(conflict-target)] DO NOTHING | DO UPDATE ...]

INSERT INTO table-name [(column-name [, column-name ...])]
VALUES (expression [, expression ...]) [, ...]
ON CONFLICT [(conflict-target)] DO UPDATE
    SET column-name = expression [, column-name = expression ...]
    [WHERE expression]
[ON CONFLICT [(conflict-target)] DO NOTHING | DO UPDATE ...]

```

 Where `conflict-target` is:

```
(column-name [, column-name ...]) [WHERE expression]

```

### Description

 Place an upsert clause at the end of an `INSERT` statement to deal with constraint violations without failing outright. It offers two actions: `DO NOTHING` skips the conflicting row without noise, and `DO UPDATE` changes the existing row in place.

### DO NOTHING

 `DO NOTHING` makes `INSERT` ignore any row that would violate the named constraint. It raises no error and keeps the existing row as is. With a conflict target, only violations of that constraint are skipped. Without a conflict target, any uniqueness conflict is skipped; NOT NULL, CHECK and foreign-key failures are not handled by UPSERT.

```
INSERT INTO users (id, name) VALUES (1, 'Alice')
ON CONFLICT DO NOTHING;

```

### DO UPDATE

 `DO UPDATE` makes `INSERT` revise the existing row when a constraint violation happens. The `SET` clause identifies the columns to change and the values to write. `DO UPDATE` requires a conflict target.

```
INSERT INTO counters (name, value) VALUES ('hits', 1)
ON CONFLICT (name) DO UPDATE
    SET value = counters.value + 1;

```

### Conflict Target

 The conflict target states which `UNIQUE` or `PRIMARY KEY` constraint the clause handles. It is written as a parenthesized list of column names, and that list must exactly match an existing unique constraint on the table. For partial unique indexes, meaning indexes with a `WHERE` clause, the conflict target also needs a `WHERE` clause matching the index condition.

```
-- Given: CREATE UNIQUE INDEX idx ON t(a, b) WHERE c > 0
INSERT INTO t VALUES (1, 2, 3)
ON CONFLICT (a, b) WHERE c > 0 DO NOTHING;

```

### The `excluded` Table

 Within the `SET` and `WHERE` clauses of `DO UPDATE`, the special `excluded` table holds the values attempted by the insert. Update expressions can therefore refer to the current row through the table name and to the candidate row through `excluded`.

| Reference | Meaning |
| --- | --- |
| `table-name.column` | Current column value from the existing row |
| `excluded.column` | The attempted insert value that produced the conflict |

```
INSERT INTO products (sku, name, price) VALUES ('A001', 'Widget', 12.99)
ON CONFLICT (sku) DO UPDATE
    SET name = excluded.name,
        price = excluded.price;

```
