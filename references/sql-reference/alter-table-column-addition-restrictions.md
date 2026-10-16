# Alter table column addition restrictions

[Context index](alter-analyze-attach-and-domain-reference.md) · [Setup and preceding context](alter-table-and-column-renaming.md) · [Continue](alter-table-addition-and-drop-restrictions.md)

### Restrictions

 A column rename fails if:
- The table has no column by that name.
- The requested column name duplicates a column already in the same table.
- A trigger on the table refers to the column with a qualified name, for example `table_name.column_name`, inside its body.
- The column is referenced from a trigger's WHEN clause.

### Example

```
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    product_name TEXT NOT NULL,
    price REAL
);

CREATE INDEX idx_name ON products(product_name);

ALTER TABLE products RENAME COLUMN product_name TO name;

-- The index is automatically updated
-- Queries now use the new column name
SELECT name, price FROM products;

```

### ADD COLUMN

 Add a column to a table that already exists. The added column is always placed last.

```
ALTER TABLE table-name ADD COLUMN column-name type [constraints];

```

### Restrictions

 Columns introduced with ADD COLUMN must meet these requirements:

| Requirement | Reason |
| --- | --- |
| No `PRIMARY KEY` constraint | A table's primary key cannot be modified after creation |
| No `UNIQUE` constraint | A UNIQUE column addition would need a scan of every existing row |
| `NOT NULL` requires a non-NULL default | Every existing row must receive a valid value for the added column |
| The default must be a constant expression | Allowed defaults are limited to literal values, signed literals, and parenthesized constant expressions. Functions such as `random()` are disallowed |
| `CURRENT_TIME`, `CURRENT_DATE`, `CURRENT_TIMESTAMP` defaults require an empty table | Those non-deterministic values cannot be applied backward to rows that already exist |
| CHECK constraints are allowed only when the table is empty | Existing rows cannot be used to validate the constraint |
| No duplicate column name | The table must not already contain that column name |
| No `GENERATED ALWAYS AS` | ALTER TABLE cannot add generated columns |
| STRICT tables must specify a type explicitly | The type assigned to the column must be a valid STRICT type |

### Supported Column Constraints

 Added columns support these constraints:
- `NOT NULL`, when paired with a non-NULL default
- `DEFAULT expression`
- `CHECK (expression)`
- `REFERENCES` (foreign key)
- `COLLATE`
