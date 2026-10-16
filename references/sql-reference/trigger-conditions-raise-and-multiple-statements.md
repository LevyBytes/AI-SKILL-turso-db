# Trigger conditions raise and multiple statements

[Context index](trigger-definition-reference.md) · [Setup and preceding context](trigger-syntax-timing-and-row-references.md) · [Continue](trigger-audit-validation-and-cascade-examples.md)

### WHEN Clause

 The optional `WHEN` clause limits execution to rows that match its expression. That expression may read columns through `NEW` and `OLD`.

```
-- Only fire when the status column actually changes
CREATE TRIGGER log_status_change
    AFTER UPDATE OF status ON orders
    WHEN OLD.status IS NOT NEW.status
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
    AFTER UPDATE OF price, quantity ON line_items
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
