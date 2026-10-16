# Virtual table number and date series

[Context index](virtual-tables-delete-and-drop-reference.md) · [Setup and preceding context](virtual-table-modules-and-csv-files.md)

### Generate a Sequence of Integers

```
-- Use generate_series as a table-valued function
SELECT value FROM generate_series(1, 10);
-- value
-- 1
-- 2
-- ...
-- 10

-- Use with a step argument
SELECT value FROM generate_series(0, 100, 10);
-- value
-- 0
-- 10
-- 20
-- ...
-- 100

```

### Generate a Date Series with generate_series

```
-- Generate dates for the next 7 days
SELECT date('now', '+' || value || ' days') AS day
FROM generate_series(0, 6);

```

### See Also

- [SELECT](https://docs.turso.tech/sql-reference/statements/select) when you need to query virtual tables
- [Compatibility](https://docs.turso.tech/sql-reference/compatibility) for supported modules and related compatibility details
