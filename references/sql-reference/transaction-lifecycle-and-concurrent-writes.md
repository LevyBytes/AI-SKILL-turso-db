# Transaction lifecycle and concurrent writes

[Context index](select-and-transaction-reference.md) · [Setup and preceding context](transaction-begin-commit-and-rollback.md) · [Continue](concurrent-transaction-example.md)

### Implicit Transactions

 If no explicit transaction is active, Turso runs each individual statement inside an implicit transaction. This is autocommit mode. A statement is automatically committed when it succeeds and automatically rolled back when it fails.

```
-- Each of these runs in its own implicit transaction
INSERT INTO logs (msg) VALUES ('first');
INSERT INTO logs (msg) VALUES ('second');
-- If the second INSERT fails, the first INSERT is still committed

```

### Transaction Lifecycle

 A typical transaction lifecycle:

```
-- 1. Begin the transaction
BEGIN;

-- 2. Perform operations
UPDATE inventory SET quantity = quantity - 1 WHERE product_id = 42;
INSERT INTO orders (product_id, customer_id) VALUES (42, 7);

-- 3a. Commit if everything succeeded
COMMIT;

-- OR 3b. Rollback if something went wrong
ROLLBACK;

```

 Turso does not support SAVEPOINT or nested transactions. A connection may have at most one active transaction at any time.

### BEGIN CONCURRENT

 **Turso Extension**: BEGIN CONCURRENT is specific to Turso and is not available in standard SQLite. MVCC mode is required. BEGIN CONCURRENT starts an optimistic concurrent write transaction, allowing multiple connections to write at the same time.

### Syntax

```
BEGIN CONCURRENT [TRANSACTION];

```

### Requirements

 BEGIN CONCURRENT requires MVCC mode. Turn it on by setting the journal mode before any transactions are opened:

```
PRAGMA journal_mode = mvcc;

```

### How It Works

 A concurrent transaction uses a database snapshot captured when BEGIN CONCURRENT starts. Each connection reads its own snapshot and writes independently. On COMMIT, Turso checks whether another transaction modified the same rows after that snapshot:
- If no conflict exists, the transaction commits successfully.
- If Turso detects a write-write conflict, COMMIT fails, and the transaction must be rolled back and retried.
 This gives **snapshot isolation**: every transaction sees a consistent database view from its start time, and other writers are not blocked.
