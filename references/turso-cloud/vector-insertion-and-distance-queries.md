# Vector insertion and distance queries

[Context index](vectors-attach-branches-and-edge-replicas.md) · [Setup and preceding context](vector-types-storage-and-functions.md) · [Continue](vector-index-creation-and-settings.md)

### Vectors usage
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

### Step 3: Perform a vector similarity search

Queries can now combine vector operations with standard SQLite data:

```
SELECT title,
       vector_extract(embedding),
       vector_distance_cos(embedding, vector32('[0.064, 0.777, 0.661, 0.687]')) AS distance
FROM movies
ORDER BY distance ASC;

```

### Understanding Distance Results

 `vector_distance_cos` computes cosine distance, defined as:
- Cosine Distance = 1 - [Cosine Similarity](https://en.wikipedia.org/wiki/Cosine_similarity)
 Cosine distance has a range from 0 to 2:
- Values near 0 mean the vectors are almost identical or exactly the same.
- Values near 1 mean the vectors are orthogonal, or perpendicular.
- Values near 2 mean the vectors point in opposite directions.
 Because of floating-point arithmetic precision, very small negative values near zero, such as `-10^-14`, can sometimes appear. Treat them as effectively zero, meaning an exact or nearly exact vector match.

```
SELECT vector_distance_cos('[1000]', '[1000]');
-- Output: -2.0479999918166e-09

```

### Vector Limitations

- 1-bit `FLOAT1BIT` vectors do **not support** Euclidean distance
- LibSQL operations are limited to vectors with 65536 dimensions or fewer

### Indexing

 Nearest neighbors (NN) queries are widely used in AI-powered applications; [RAG](https://en.wikipedia.org/wiki/Retrieval-augmented_generation) uses NN queries to pull relevant information, and recommendation engines can use embedding similarity to suggest items. To accelerate approximate nearest neighbors queries on tables with vector columns, LibSQL implements the [DiskANN](https://turso.tech/blog/approximate-nearest-neighbor-search-with-diskann-in-libsql) algorithm. DiskANN favors speed by trading away some accuracy, so on large tables LibSQL may return neighbors that are slightly suboptimal.
