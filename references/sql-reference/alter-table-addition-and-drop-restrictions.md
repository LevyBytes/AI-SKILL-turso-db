# Alter table addition and drop restrictions

[Context index](alter-analyze-attach-and-domain-reference.md) · [Setup and preceding context](alter-table-and-column-renaming.md) · [Previous phase](alter-table-column-addition-restrictions.md) · [Continue](alter-table-column-removal-and-schema-evolution.md)

### Examples

#### Add a simple column

```
ALTER TABLE users ADD COLUMN phone TEXT;

```

#### Add a column with a default value

```
ALTER TABLE orders ADD COLUMN priority INTEGER DEFAULT 0;

```

#### Add a NOT NULL column with a default

```
ALTER TABLE products ADD COLUMN in_stock INTEGER NOT NULL DEFAULT 1;

```

#### Add a column with a foreign key

```
ALTER TABLE orders ADD COLUMN warehouse_id INTEGER REFERENCES warehouses(id);

```

#### Add a column on a STRICT table

```
CREATE TABLE metrics (
    id INTEGER PRIMARY KEY,
    value REAL
) STRICT;

-- Must specify a valid STRICT type
ALTER TABLE metrics ADD COLUMN label TEXT DEFAULT 'unknown';

```

### DROP COLUMN

 Delete a column that already exists in a table.

```
ALTER TABLE table-name DROP COLUMN column-name;

```

### Restrictions

 A column drop is prohibited when any condition below applies:

| Condition | Error |
| --- | --- |
| The column is a PRIMARY KEY, or it belongs to one | Cannot drop PRIMARY KEY column |
| The column carries a UNIQUE constraint | Cannot drop UNIQUE column |
| An index references the column | Cannot drop indexed column |
| An expression index references the column | Column used in an expression index cannot be dropped |
| A partial index WHERE clause names the column | Column used in a partial index cannot be dropped |
| A CHECK constraint on another column or on the table refers to the column | Column used in a CHECK constraint cannot be dropped |
| A foreign key in another table references the column | Column used in a foreign key cannot be dropped |
| The table contains exactly one column | The last remaining column cannot be dropped |
| A trigger or view includes the column | Column referenced by a trigger or view cannot be dropped |
