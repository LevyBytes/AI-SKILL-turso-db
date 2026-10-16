# Vector index query workflow

[Context index](vectors-attach-branches-and-edge-replicas.md) · [Setup and preceding context](vector-types-storage-and-functions.md) · [Previous phase](vector-index-creation-and-settings.md)

### Index usage
### Step 1: Create a table

Start by declaring the vector-storage column with datatype `F32_BLOB`:

```
CREATE TABLE movies (
  title     TEXT,
  year      INT,
  embedding F32_BLOB(4) -- 4-dimensional f32 vector
);

```

The `(4)` value sets vector dimensionality, so every vector stored in this column has exactly 4 components.

### Step 2: Generate and insert embeddings

After creating embeddings for the data through an LLM, insert those values into the table:

```
INSERT INTO movies (title, year, embedding)
VALUES
  ('Napoleon', 2023, vector32('[0.800, 0.579, 0.481, 0.229]')),
  ('Black Hawk Down', 2001, vector32('[0.406, 0.027, 0.378, 0.056]')),
  ('Gladiator', 2000, vector32('[0.698, 0.140, 0.073, 0.125]')),
  ('Blade Runner', 1982, vector32('[0.379, 0.637, 0.011, 0.647]'));

```

Embeddings can be generated with popular tools including [LangChain](https://www.langchain.com), [Hugging Face](https://huggingface.co), or [OpenAI](https://turso.tech/blog/how-to-generate-and-store-openai-vector-embeddings-with-turso).

### Step 3: Create an Index

Create an index with the `libsql_vector_idx` function:

```
CREATE INDEX movies_idx ON movies(libsql_vector_idx(embedding));

```

This produces an index optimized for similarity searches over vectors stored in the `embedding` column. The `libsql_vector_idx` marker function is **required**; libSQL uses it to tell `ANN`-indices apart from ordinary B-Tree indices.

### Step 4: Query the indexed table

```
SELECT title, year
FROM vector_top_k('movies_idx', vector32('[0.064, 0.777, 0.661, 0.687]'), 3)
JOIN movies ON movies.rowid = id
WHERE year >= 2020;

```

This query uses the `vector_top_k` [table-valued function](https://www.sqlite.org/vtab.html#table_valued_functions) and the index to efficiently locate the top 3 vectors most similar to `[0.064, 0.777, 0.661, 0.687]`.

### Index limitations

- Vector indexes work only for tables **with** `ROWID` or with a singular `PRIMARY KEY`; a composite `PRIMARY KEY` without `ROWID` is not supported
