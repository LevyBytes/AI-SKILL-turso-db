# Explain bytecode and query plan columns

[Context index](explain-insert-and-replace-reference.md) · [Continue](explain-plan-comparisons-joins-and-subqueries.md)

## EXPLAIN

 EXPLAIN reports how Turso would run a SQL statement. It supports two modes: `EXPLAIN` returns virtual machine bytecode, while `EXPLAIN QUERY PLAN` summarizes the query execution plan.

### Syntax

```
EXPLAIN statement;
EXPLAIN QUERY PLAN statement;

```

| Parameter | Type | Description |
| --- | --- | --- |
| statement | SQL | Any SQL statement, such as SELECT, INSERT, UPDATE, DELETE, and similar statements. |

### EXPLAIN

 Adding the `EXPLAIN` prefix makes Turso report the virtual machine (VDBE) opcode sequence that would run the statement, without actually running that statement.

```
EXPLAIN SELECT * FROM users WHERE id = 1;
-- addr | opcode     | p1 | p2 | p3 | p4       | p5
-- 0    | Init       | 0  | 9  | 0  |          | 0
-- 1    | OpenRead   | 0  | 2  | 0  | 3        | 0
-- 2    | SeekRowid  | 0  | 8  | 1  |          | 0
-- ...

```

### Output Columns

| Column | Description |
| --- | --- |
| addr | Instruction address (sequential integer) |
| opcode | Name of the operation, such as OpenRead, SeekRowid, Column, or ResultRow. |
| p1 | First operand |
| p2 | Second operand |
| p3 | Third operand |
| p4 | The fourth operand, commonly text such as a table name or collation. |
| p5 | Fifth operand (flags) |

### EXPLAIN QUERY PLAN

 Use `EXPLAIN QUERY PLAN` before a statement to get a high-level summary of the optimizer's chosen execution approach. For performance analysis, this is usually easier to interpret than raw EXPLAIN output.

```
EXPLAIN QUERY PLAN SELECT * FROM users WHERE name = 'Alice';
-- id | parent | notused | detail
-- 2  | 0      | 0       | SCAN users

```

```
CREATE INDEX idx_name ON users(name);
EXPLAIN QUERY PLAN SELECT * FROM users WHERE name = 'Alice';
-- id | parent | notused | detail
-- 3  | 0      | 0       | SEARCH users USING INDEX idx_name (name=?)

```

### Output Columns

| Column | Description |
| --- | --- |
| id | Distinct step identifier. |
| parent | Parent step id, with 0 meaning top-level. |
| notused | Reserved for future use (always 0) |
| detail | Readable explanation of what this execution step does. |
