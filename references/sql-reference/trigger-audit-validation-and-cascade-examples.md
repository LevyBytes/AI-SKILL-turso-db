# Trigger audit validation and cascade examples

[Context index](trigger-definition-reference.md) · [Setup and preceding context](trigger-syntax-timing-and-row-references.md) · [Previous phase](trigger-conditions-raise-and-multiple-statements.md)

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

INSERT INTO accounts (id, owner, balance) VALUES (1, 'Alice', 0);

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
