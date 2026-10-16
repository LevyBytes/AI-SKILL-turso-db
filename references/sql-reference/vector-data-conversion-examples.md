# Vector data conversion examples

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](vector-float-creation-functions.md) · [Previous phase](vector-storage-and-similarity-examples.md)

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
