# Create Trigger

> Sql Reference reference. Original prose; identifiers preserved verbatim.

### CREATE TRIGGER

 Because this capability is experimental, it must be [enabled before use](https://docs.turso.tech/sql-reference/experimental-features). Use a trigger to run one or more SQL statements automatically when table rows are inserted, updated, or deleted.

### Syntax

```
CREATE [TEMPORARY] TRIGGER [IF NOT EXISTS] [schema-name.]trigger-name
    [BEFORE | AFTER | INSTEAD OF]
    {INSERT | UPDATE [OF column-name, ...] | DELETE}
    ON table-name
    [FOR EACH ROW]
    [WHEN expression]
BEGIN
    statement;
    [statement; ...]
END;

```

### Description

 A trigger stores SQL statements that are invoked automatically when a chosen data-change event happens on a table or view. Those statements run in the transaction that caused the trigger to fire; rolling that transaction back also undoes the trigger work.

### Parameters

| Parameter | Description |
| --- | --- |
| `TEMPORARY` | Places the trigger in a temporary database. Only the current connection can see it, and it is removed when that connection ends. `TEMP` is also valid. |
| `IF NOT EXISTS` | Suppresses the error that would otherwise occur when another trigger already uses the requested name. |
| `schema-name` | Identifies the attached database where the trigger is stored. If omitted, the main database is used. It cannot be combined with `TEMPORARY`. |
| `trigger-name` | Name assigned to the trigger; it must be unique within that database. |
| `BEFORE` / `AFTER` / `INSTEAD OF` | Controls whether the trigger runs before, after, or instead of the event that activates it. The default is `BEFORE`. |
| `INSERT` / `UPDATE` / `DELETE` | Specifies which data-change operation causes the trigger to run. |
| `OF column-name, ...` | Used only on UPDATE triggers; it limits firing to updates that modify the listed columns. |
| `table-name` | Names the table monitored by the trigger, or the view when defining INSTEAD OF triggers. |
| `FOR EACH ROW` | Runs the trigger one time for each row changed. This row-level behavior is the sole supported mode and remains the default when unspecified. |
| `WHEN expression` | Optional predicate used to decide whether the trigger body should run for a row; execution happens only when the expression is true. |

### Trigger Timing

### BEFORE Triggers

 A `BEFORE` trigger runs prior to the event changing the row. BEFORE triggers are useful when data must be checked or adjusted before storage.
- `NEW` exposes the values that will be stored. In INSERT triggers, `NEW` is the incoming row. In UPDATE triggers, `NEW` holds the post-update values.
- `OLD` can be used in UPDATE and DELETE triggers; it represents the row values as they existed before the change.
- When a BEFORE trigger raises an error, processing for that row stops and the operation that caused it is canceled.

### AFTER Triggers

 An `AFTER` trigger runs once the row has already been changed by the triggering statement. Typical uses include logging, audit trails, and follow-on changes in other tables.
- `NEW` and `OLD` keep the same meanings here that they have in BEFORE triggers.
- By the time the trigger body runs, the row change has already been applied.

### INSTEAD OF Triggers

 An `INSTEAD OF` trigger is valid only for a view. It runs as the replacement for the INSERT, UPDATE, or DELETE that would target the view, which can make the view writable.
- The INSERT, UPDATE, or DELETE against the view itself is not performed. Logic in the trigger body must apply the intended changes to the base tables.

### Row References

 Within a trigger body, the special row references `NEW` and `OLD` let statements read column values.
| Reference | INSERT | UPDATE | DELETE |
| --- | --- | --- | --- |
| `NEW.column` | Value being inserted | Updated value | Not available |
| `OLD.column` | Not available | Value before update | Value being deleted |

```
-- Access individual columns
NEW.email
OLD.status

```

### WHEN Clause

 The optional `WHEN` clause limits execution to rows that match its expression. That expression may read columns through `NEW` and `OLD`.

```
-- Only fire when the status column actually changes
CREATE TRIGGER log_status_change
    AFTER UPDATE OF status ON orders
    WHEN OLD.status != NEW.status
BEGIN
    INSERT INTO order_log (order_id, old_status, new_status, changed_at)
    VALUES (NEW.id, OLD.status, NEW.status, datetime('now'));
END;

```

### UPDATE OF Columns

 For UPDATE triggers, the `OF` clause narrows firing to changes on selected columns. If `OF` is absent, any UPDATE on the table can cause the trigger to run.

```
-- Only fires when price or quantity changes, not when name changes
CREATE TRIGGER recalc_total
    BEFORE UPDATE OF price, quantity ON line_items
BEGIN
    UPDATE line_items SET total = NEW.price * NEW.quantity WHERE id = NEW.id;
END;

```

### RAISE Function

 Use `RAISE` inside a trigger body, or in other valid contexts, when execution should stop with an error. The function supports four forms:
| Form | Behavior |
| --- | --- |
| `RAISE(IGNORE)` | Stops processing the current row by skipping both the rest of the trigger body and the statement that fired it. Execution then moves on to the following row. |
| `RAISE(ABORT, message)` | Stops the current statement and undoes changes from that statement, while leaving earlier transaction changes intact. This matches the normal error-handling behavior. |
| `RAISE(ROLLBACK, message)` | Stops the current statement and reverses the full transaction. |
| `RAISE(FAIL, message)` | Stops the current statement. Work already completed by that statement, such as earlier rows, remains, but the current row and later rows are skipped. |

```
CREATE TRIGGER validate_age
    BEFORE INSERT ON users
BEGIN
    SELECT RAISE(ABORT, 'age must be positive')
    WHERE NEW.age <= 0;
END;

```

### Multiple Statements

 A trigger body may include several SQL statements, divided with semicolons. They run sequentially and remain part of the same transaction.

```
CREATE TRIGGER on_user_delete
    AFTER DELETE ON users
BEGIN
    DELETE FROM user_preferences WHERE user_id = OLD.id;
    DELETE FROM user_sessions WHERE user_id = OLD.id;
    INSERT INTO audit_log (action, entity, entity_id, performed_at)
    VALUES ('delete', 'user', OLD.id, datetime('now'));
END;

```

### Examples

### Audit Logging Trigger

 Record every table change in an audit log.

```
CREATE TABLE accounts (
    id INTEGER PRIMARY KEY,
    owner TEXT NOT NULL,
    balance REAL NOT NULL DEFAULT 0
);

CREATE TABLE account_audit (
    id INTEGER PRIMARY KEY,
    account_id INTEGER NOT NULL,
    action TEXT NOT NULL,
    old_balance REAL,
    new_balance REAL,
    changed_at TEXT NOT NULL
);

CREATE TRIGGER audit_balance_change
    AFTER UPDATE OF balance ON accounts
BEGIN
    INSERT INTO account_audit (account_id, action, old_balance, new_balance, changed_at)
    VALUES (NEW.id, 'update', OLD.balance, NEW.balance, datetime('now'));
END;

-- This UPDATE automatically creates an audit row
UPDATE accounts SET balance = balance + 100 WHERE id = 1;

```

### Validation Trigger

 Check business constraints before storing the data.

```
CREATE TABLE reservations (
    id INTEGER PRIMARY KEY,
    room_id INTEGER NOT NULL,
    check_in TEXT NOT NULL,
    check_out TEXT NOT NULL
);

CREATE TRIGGER validate_reservation
    BEFORE INSERT ON reservations
BEGIN
    SELECT RAISE(ABORT, 'check_out must be after check_in')
    WHERE NEW.check_out <= NEW.check_in;
END;

```

### Cascading Update Trigger

 Propagate changes to related tables.

```
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    category_id INTEGER
);

CREATE TABLE categories (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
);

-- When a category is deleted, unset the category on all associated products
CREATE TRIGGER clear_product_category
    BEFORE DELETE ON categories
BEGIN
    UPDATE products SET category_id = NULL WHERE category_id = OLD.id;
END;

```

### See Also

- Use [DROP TRIGGER](https://docs.turso.tech/sql-reference/statements/drop-trigger) when a trigger needs to be removed.
- Use [CREATE VIEW](https://docs.turso.tech/sql-reference/statements/create-view) when working with views that depend on INSTEAD OF triggers.
- [INSERT](https://docs.turso.tech/sql-reference/statements/insert), [UPDATE](https://docs.turso.tech/sql-reference/statements/update), and [DELETE](https://docs.turso.tech/sql-reference/statements/delete) are the statements that can activate triggers.
