# Vector, Window

> Sql Reference reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Vector Functions; Window Functions.

## Vector Functions

### Vector Functions

 **Turso Extension**: Turso includes vector functions for handling vector embeddings from SQL. They are Turso-specific additions rather than SQLite standard features. With these built-ins, embeddings can be saved and searched in SQL. The vector values are encoded as compact BLOB data and can be evaluated with distance functions for similarity search.

---

### Vector Creation

### vector32

 Builds a 32-bit floating-point vector from a JSON numeric array.

```
vector32(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | JSON numeric array, for example `'[1.0, 2.0, 3.0]'` |

 **Returns:** BLOB - a vector encoded in 32-bit float binary form. This is the typical vector representation, balancing precision with storage size. Each dimension takes 4 bytes.

```
SELECT vector32('[1.0, 2.0, 3.0]');
-- (BLOB: 12 bytes)

-- Store embeddings in a table
CREATE TABLE documents (
    id INTEGER PRIMARY KEY,
    content TEXT,
    embedding BLOB
);

INSERT INTO documents VALUES (1, 'Introduction to databases', vector32('[0.1, 0.3, 0.5, 0.7]'));

```

### vector64

 Builds a 64-bit floating-point vector from a JSON numeric array.

```
vector64(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | JSON numeric array, for example `'[1.0, 2.0, 3.0]'` |

 **Returns:** BLOB - a vector encoded in 64-bit float binary form. Choose this representation for greater precision. Each dimension takes 8 bytes.

```
SELECT vector64('[1.0, 2.0, 3.0]');
-- (BLOB: 24 bytes)

```

### vector

 Builds a vector with the default representation, which is 32-bit float.

```
vector(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | A JSON array of numbers |

 **Returns:** BLOB - a vector encoded in the default binary representation, 32-bit float.

```
SELECT vector('[1.0, 2.0, 3.0]');

```

### vector8

 Builds an 8-bit quantized vector from a JSON numeric array.

```
vector8(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | JSON array containing integer values from 0-255 |

 **Returns:** BLOB - a vector encoded as 8-bit integer binary data. Use it for approximate vectors when compact storage is important. Each dimension takes 1 byte.

```
SELECT vector8('[128, 64, 255, 0]');

```

### vector1bit

 Builds a 1-bit binary vector from a JSON array.

```
vector1bit(json_array)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_array` | TEXT | JSON array containing 0s and 1s |

 **Returns:** BLOB - a vector encoded in 1-bit binary form. Use it when the representation must be extremely compact. A single byte stores 8 dimensions.

```
SELECT vector1bit('[1, 0, 1, 1, 0, 0, 1, 0]');

```

---

### Distance Functions

 Distance functions measure how far apart two vectors are. Lower results mean the vectors are more alike.

### vector_distance_cos

 Calculates cosine distance for two vectors, expressed as `1 - cosine_similarity`. A value of 0 indicates the same direction, while 1 indicates orthogonality.

```
vector_distance_cos(v1, v2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1` | BLOB | First vector |
| `v2` | BLOB | Second vector, with the same dimension count as `v1` |

 **Returns:** REAL - the cosine distance for the pair of vectors.

```
SELECT vector_distance_cos(
    vector32('[1.0, 0.0, 0.0]'),
    vector32('[0.0, 1.0, 0.0]')
);
-- 1.0 (orthogonal vectors)

SELECT vector_distance_cos(
    vector32('[1.0, 0.0]'),
    vector32('[1.0, 0.0]')
);
-- 0.0 (identical vectors)

```

### vector_distance_l2

 Calculates the Euclidean (L2) distance for two vectors. If the result is 0, the vectors match exactly.

```
vector_distance_l2(v1, v2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1` | BLOB | First vector |
| `v2` | BLOB | Second vector, with the same dimension count as `v1` |

 **Returns:** REAL - the Euclidean distance for the pair of vectors.

```
SELECT vector_distance_l2(
    vector32('[1.0, 0.0, 0.0]'),
    vector32('[0.0, 1.0, 0.0]')
);
-- 1.4142135623730951

SELECT vector_distance_l2(
    vector32('[3.0, 4.0]'),
    vector32('[0.0, 0.0]')
);
-- 5.0

```

### vector_distance_dot

 Calculates distance using the negative dot product of two vectors. Because the dot product is negated, lower and more negative results correspond to stronger similarity.

```
vector_distance_dot(v1, v2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1` | BLOB | First vector |
| `v2` | BLOB | Second vector, with the same dimension count as `v1` |

 **Returns:** REAL - the negative dot product for the pair of vectors.

```
SELECT vector_distance_dot(
    vector32('[1.0, 2.0, 3.0]'),
    vector32('[4.0, 5.0, 6.0]')
);
-- -32.0 (dot product is 32)

```

### vector_distance_jaccard

 Calculates Jaccard distance for two binary vectors. It is intended primarily for vectors produced with `vector1bit`.

```
vector_distance_jaccard(v1, v2)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1` | BLOB | First vector |
| `v2` | BLOB | Second vector, with the same dimension count as `v1` |

 **Returns:** REAL - the Jaccard distance for the pair of vectors.

---

### Utility Functions

### vector_extract

 Turns a vector BLOB into its JSON text form.

```
vector_extract(vector_blob)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `vector_blob` | BLOB | Vector produced by `vector32`, `vector64`, or a different vector function |

 **Returns:** TEXT - a JSON array string containing the vector components.

```
SELECT vector_extract(vector32('[1.5, 2.5, 3.5]'));
-- [1.500000,2.500000,3.500000]

```

### vector_concat

 Joins two or more vectors into one vector.

```
vector_concat(v1, v2, ...)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `v1, v2, ...` | BLOB | Two or more vectors to join. Every vector must share the same type. |

 **Returns:** BLOB - a new vector whose dimensions are the input vectors laid out sequentially.

```
SELECT vector_extract(
    vector_concat(
        vector32('[1.0, 2.0]'),
        vector32('[3.0, 4.0]')
    )
);
-- [1.000000,2.000000,3.000000,4.000000]

```

### vector_slice

 Takes a continuous segment from a vector.

```
vector_slice(vector_blob, start, end)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `vector_blob` | BLOB | The source vector |
| `start` | INTEGER | Start index (zero-based, inclusive) |
| `end` | INTEGER | End index (exclusive) |

 **Returns:** BLOB - a new vector containing the dimensions beginning at `start` and ending before `end - 1`.

```
SELECT vector_extract(
    vector_slice(vector32('[10.0, 20.0, 30.0, 40.0, 50.0]'), 1, 4)
);
-- [20.000000,30.000000,40.000000]

```

---

### Examples

### Creating a table with vector embeddings

```
CREATE TABLE articles (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    content TEXT,
    embedding BLOB
);

INSERT INTO articles VALUES (
    1,
    'Introduction to Machine Learning',
    'Machine learning is a subset of artificial intelligence...',
    vector32('[0.12, -0.34, 0.56, 0.78, -0.11, 0.45, -0.23, 0.67]')
);

INSERT INTO articles VALUES (
    2,
    'Database Design Patterns',
    'Relational databases organize data into tables...',
    vector32('[0.82, 0.15, -0.44, 0.23, 0.61, -0.33, 0.09, 0.77]')
);

INSERT INTO articles VALUES (
    3,
    'Neural Networks Explained',
    'Neural networks are computing systems inspired by biological neural networks...',
    vector32('[0.14, -0.28, 0.62, 0.71, -0.09, 0.51, -0.18, 0.59]')
);

```

### Similarity search with ORDER BY distance

```
-- Find articles most similar to a query embedding using cosine distance
SELECT
    id,
    title,
    vector_distance_cos(embedding, vector32('[0.1, -0.3, 0.5, 0.8, -0.1, 0.4, -0.2, 0.6]')) AS distance
FROM articles
ORDER BY distance ASC
LIMIT 5;

```

### Cosine distance vs. L2 distance

 Cosine distance compares vector direction and disregards magnitude. L2 distance compares actual spatial separation.

```
-- Cosine distance: direction matters, not magnitude
SELECT vector_distance_cos(
    vector32('[1.0, 0.0]'),
    vector32('[100.0, 0.0]')
);
-- 0.0 (same direction, cosine distance is 0)

-- L2 distance: magnitude matters
SELECT vector_distance_l2(
    vector32('[1.0, 0.0]'),
    vector32('[100.0, 0.0]')
);
-- 99.0 (very far apart in absolute terms)

```

 Choose cosine distance when vector direction is what matters, such as semantic similarity of text embeddings. Choose L2 distance when exact position is important, such as geospatial coordinates or image feature vectors.

### Working with vector data

```
-- Inspect the contents of a stored vector
SELECT vector_extract(embedding) FROM articles WHERE id = 1;

-- Concatenate vectors from two different models
SELECT vector_extract(
    vector_concat(
        (SELECT embedding FROM articles WHERE id = 1),
        (SELECT embedding FROM articles WHERE id = 2)
    )
);

-- Extract the first 4 dimensions of an embedding
SELECT vector_extract(
    vector_slice(embedding, 0, 4)
) FROM articles WHERE id = 1;
-- [0.120000,-0.340000,0.560000,0.780000]

```

 Vector indexes are not supported yet. Similarity queries currently scan the table linearly. On large datasets, use a WHERE clause when possible to narrow the rows searched.

### See Also

- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) describes index types, including FTS
- [Data Types](https://docs.turso.tech/sql-reference/data-types) explains Turso handling for BLOB values

## Window Functions

### Window Functions

 A window function returns a value for each input row while also looking at rows in that row's window. With GROUP BY aggregates, rows are grouped down into fewer output rows; with a window calculation, the original rows remain visible. Turso currently lets aggregate functions run through OVER with the default frame rules. It does not yet support explicit ROWS, RANGE, or GROUPS frame bounds, and it also does not provide dedicated window functions such as row_number, rank, dense_rank, ntile, lag, lead, first_value, last_value, and nth_value.

### Syntax

```
aggregate_function(expression) OVER (
    [PARTITION BY expression [, ...]]
    [ORDER BY expression [ASC | DESC] [, ...]]
)

```

| Clause | Description |
| --- | --- |
| `aggregate_function` | Any supported aggregate function: `count`, `sum`, `avg`, `min`, `max`, `total`, `group_concat` |
| `OVER (...)` | Specifies the window used by the function |
| `PARTITION BY` | Splits the result set into partitions. The function runs separately inside each partition. If this clause is left out, the full result set is treated as one partition |
| `ORDER BY` | Specifies row ordering within each partition. This ordering controls which rows belong to the frame for each calculation |

### Default Frame

 If ORDER BY is present, this is the default frame:

```
RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW

```

 The function therefore evaluates rows from the beginning of the partition through the current row, including peers with matching ORDER BY values because the frame mode is RANGE. Without ORDER BY, the default frame spans the full partition.

### Supported Aggregate Functions as Window Functions

 Adding an OVER clause lets any aggregate function operate as a window function.
| Function | Description |
| --- | --- |
| `count(*)` | Count of rows in the frame |
| `count(expression)` | Count of non-NULL entries in the frame |
| `sum(expression)` | Total of non-NULL entries in the frame |
| `avg(expression)` | Mean of non-NULL entries in the frame |
| `min(expression)` | Minimum value in the frame |
| `max(expression)` | Maximum value in the frame |
| `total(expression)` | Total as REAL, returning 0.0 for empty frames rather than NULL |
| `group_concat(expression, separator)` | Frame values joined together |

### PARTITION BY

 PARTITION BY separates rows into groups. For each partition, the window function starts over and computes its result independently.

```
SELECT
    department,
    name,
    salary,
    SUM(salary) OVER (PARTITION BY department) AS dept_total
FROM employees;

```

| department | name | salary | dept_total |
| --- | --- | --- | --- |
| Engineering | Alice | 90000 | 250000 |
| Engineering | Bob | 85000 | 250000 |
| Engineering | Carol | 75000 | 250000 |
| Marketing | Dave | 70000 | 130000 |
| Marketing | Eve | 60000 | 130000 |

 If PARTITION BY is absent, the function uses the full result set as a single partition:

```
SELECT
    name,
    salary,
    SUM(salary) OVER () AS company_total
FROM employees;

```

### ORDER BY

 ORDER BY inside OVER sets the row order for each partition. Together with the default frame, RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW, it yields running calculations.

```
SELECT
    name,
    salary,
    SUM(salary) OVER (ORDER BY salary) AS running_total
FROM employees;

```

| name | salary | running_total |
| --- | --- | --- |
| Eve | 60000 | 60000 |
| Dave | 70000 | 130000 |
| Carol | 75000 | 205000 |
| Bob | 85000 | 290000 |
| Alice | 90000 | 380000 |

### PARTITION BY with ORDER BY

 Combine both clauses to compute running values within each group:

```
SELECT
    department,
    name,
    salary,
    SUM(salary) OVER (
        PARTITION BY department
        ORDER BY salary
    ) AS dept_running_total
FROM employees;

```

| department | name | salary | dept_running_total |
| --- | --- | --- | --- |
| Engineering | Carol | 75000 | 75000 |
| Engineering | Bob | 85000 | 160000 |
| Engineering | Alice | 90000 | 250000 |
| Marketing | Eve | 60000 | 60000 |
| Marketing | Dave | 70000 | 130000 |

### Named Windows

 The WINDOW clause creates a named window specification that several window functions in one query can reuse. This keeps the same OVER definition from being written multiple times.

```
SELECT
    department,
    name,
    salary,
    SUM(salary) OVER w AS running_total,
    AVG(salary) OVER w AS running_avg,
    COUNT(*) OVER w AS running_count
FROM employees
WINDOW w AS (PARTITION BY department ORDER BY salary)
ORDER BY department, salary;

```

 A query can declare more than one named window:

```
SELECT
    department,
    name,
    salary,
    SUM(salary) OVER dept AS dept_total,
    SUM(salary) OVER company AS company_total
FROM employees
WINDOW
    dept AS (PARTITION BY department),
    company AS ()
ORDER BY department, name;

```

### Examples

### Running Total

```
SELECT
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders;

```

### Count per Group

```
-- Show each employee alongside their department headcount
SELECT
    name,
    department,
    COUNT(*) OVER (PARTITION BY department) AS dept_size
FROM employees
ORDER BY department, name;

```

### Running Average

```
SELECT
    date,
    temperature,
    AVG(temperature) OVER (ORDER BY date) AS running_avg_temp
FROM weather_readings
ORDER BY date;

```

### Percentage of Total

```
SELECT
    product_name,
    revenue,
    ROUND(100.0 * revenue / SUM(revenue) OVER (), 2) AS pct_of_total
FROM products
ORDER BY revenue DESC;

```

### Multiple Window Functions in One Query

```
SELECT
    department,
    name,
    salary,
    MIN(salary) OVER (PARTITION BY department) AS dept_min,
    MAX(salary) OVER (PARTITION BY department) AS dept_max,
    AVG(salary) OVER (PARTITION BY department) AS dept_avg,
    salary - AVG(salary) OVER (PARTITION BY department) AS diff_from_avg
FROM employees
ORDER BY department, salary DESC;

```

### Group Concatenation over a Window

```
SELECT
    department,
    name,
    GROUP_CONCAT(name, ', ') OVER (PARTITION BY department ORDER BY name) AS names_so_far
FROM employees;

```

### Limitations

 These window function capabilities are not supported in Turso yet:
| Feature | Status |
| --- | --- |
| `row_number()` | Not supported |
| `rank()` | Not supported |
| `dense_rank()` | Not supported |
| `ntile(N)` | Not supported |
| `lag(expr, offset, default)` | Not supported |
| `lead(expr, offset, default)` | Not supported |
| `first_value(expr)` | Not supported |
| `last_value(expr)` | Not supported |
| `nth_value(expr, N)` | Not supported |
| `cume_dist()` | Not supported |
| `percent_rank()` | Not supported |
| Custom frame: `ROWS BETWEEN ...` | Not supported |
| Custom frame: `RANGE BETWEEN ... AND ...` | Not supported |
| Custom frame: `GROUPS BETWEEN ...` | Not supported |
| `EXCLUDE` clause | Not supported |
| `FILTER (WHERE ...)` on window functions | Not supported |

### See Also

- [SELECT](https://docs.turso.tech/sql-reference/statements/select) documents the complete SELECT syntax, including the WINDOW clause
- [Aggregate Functions](https://docs.turso.tech/sql-reference/functions/aggregate) provides the aggregate function reference
- [Expressions](https://docs.turso.tech/sql-reference/expressions) covers using window function results inside expressions
