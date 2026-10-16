# Update set from and conflicts

[Context index](update-upsert-and-vacuum-reference.md) · [Continue](update-where-returning-and-join-examples.md)

## UPDATE

 An `UPDATE` statement revises column values for rows already present in a table.

### Syntax

```
UPDATE [OR conflict-algorithm] table-name
SET column-name = expression [, column-name = expression ...]
[FROM from-clause]
[WHERE expression]
[RETURNING result-column [, result-column ...]]
[ORDER BY sort-expression [, sort-expression ...]]
[LIMIT expression [OFFSET expression]]

```

### Description

 `UPDATE` assigns different values to columns on rows selected by the `WHERE` condition. Without a `WHERE` clause, the statement applies to every row in the table.

### Conflict Handling

 Use the optional `OR` clause to choose the response to constraint violations during an update. If it is left out, `ABORT` is used.

| Algorithm | Behavior |
| --- | --- |
| `ABORT` | Undo the current statement and return an error. The transaction stays open. This is the default. |
| `ROLLBACK` | Undo the complete transaction and report an error. |
| `FAIL` | Return an error at the first constraint violation, while retaining changes already made by earlier rows in this statement. |
| `IGNORE` | Ignore the row that triggered the constraint violation and keep processing the rest of the rows. |
| `REPLACE` | Remove the conflicting row, then update the current row. If a NOT NULL constraint fails without a DEFAULT value, the statement falls back to `ABORT`. |

```
UPDATE OR IGNORE employees
SET department = 'Engineering'
WHERE department = 'IT';

```

### SET Clause

 With `SET`, one or more columns receive replacement values. Each assignment pairs a column name with an equals sign and an expression. That expression may use other columns from the same row, and it reads every column as it was before the update began.

```
UPDATE products
SET price = price * 1.10,
    updated_at = datetime('now');

```

 A parenthesized column list can be used to assign several columns at once:

```
UPDATE products
SET (price, updated_at) = (price * 1.10, datetime('now'));

```

### FROM Clause

 `FROM` lets an `UPDATE` pull in other tables. Joined-table columns are available in `SET` expressions and in the `WHERE` clause, which makes it possible to update one table from related rows in another.

```
UPDATE inventory
SET quantity = inventory.quantity - order_items.qty
FROM order_items
WHERE inventory.product_id = order_items.product_id
  AND order_items.order_id = 1001;

```

 If the target table is named again in `FROM`, that reference is a separate instance of the table and can be used for self-joins.
