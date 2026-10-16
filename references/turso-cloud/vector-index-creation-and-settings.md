# Vector index creation and settings

[Context index](vectors-attach-branches-and-edge-replicas.md) · [Setup and preceding context](vector-types-storage-and-functions.md) · [Previous phase](vector-insertion-and-distance-queries.md) · [Continue](vector-index-query-workflow.md)

### Vector Index

 LibSQL adds a custom index type for faster nearest neighbors queries against one fixed distance function, with cosine similarity as the default. Syntactically, this vector index is unlike ordinary application-defined B-Tree indices because the vector column must be wrapped in the `libsql_vector_idx` marker function, as shown here

```
CREATE INDEX movies_idx ON movies (libsql_vector_idx(embedding));

```

 A vector index can be used only on a column whose type is one of the vector types above. Because the vector index is integrated into LibSQL core, it receives every operation and most features available to ordinary indices:
- If the table already has data, creating an index for it automatically fills the index with that data
- Every update made to the base table is **automatically** represented in the index
- Rebuild the index from scratch with the `REINDEX movies_idx` command
- Drop the index with the `DROP INDEX movies_idx` command
- Create a [partial](https://www.sqlite.org/partialindex.html) vector index by supplying a custom filtering rule:

```
CREATE INDEX movies_idx ON movies (libsql_vector_idx(embedding))
WHERE year >= 2000;

```

### Query

 For now, a vector index is queried **explicitly** via the special `vector_top_k(idx_name, q_vector, k)` [table-valued function](https://www.sqlite.org/vtab.html#table_valued_functions). The function takes an index name, a query vector, and the number of neighbors to return. It searches for `k` approximate nearest neighbors and returns the `ROWID` values for those rows, or the `PRIMARY KEY` when the base index [does not have ROWID](https://www.sqlite.org/withoutrowid.html). The table-valued function requires the query vector to use the same vector type and dimensionality.

### Settings

 LibSQL vector indexes may receive optional settings. Pass them as variadic string parameters to `libsql_vector_idx` using the `key=value` format:

```
CREATE INDEX movies_idx
ON movies(libsql_vector_idx(embedding, 'metric=l2', 'compress_neighbors=float8'));

```

 The settings LibSQL supports at present are:

| Setting key | Value type | Description |
| --- | --- | --- |
| `metric` | `cosine` or `l2` | Distance function used when building the index. Default: `cosine` |
| `max_neighbors` | positive integer | Number of neighbors kept for each node in the DiskANN graph. Smaller values make the index use less storage but lower search precision. Default: 3 × √D, where D is the vector column dimensionality |
| `compress_neighbors` | `float1bit`, `float8`, `float16`, `floatb16`, `float32` | Vector type used to store neighbors for each node in the DiskANN graph. More compact neighbor types lower index storage while reducing search precision. Default: **no compression** (neighbors has same type as base table) |
| `alpha` | float ≥ 1 | The “Density” parameter for the general sparse neighborhood graph built during DiskANN. Lower values make the DiskANN graph sparser, which can improve query speed while reducing search precision. Default: `1.2` |
| `search_l` | positive integer | Limit on the number of neighbors visited during vector search. Lower values make the search query faster while reducing search precision. Default: `200` |
| `insert_l` | positive integer | Limit on the number of neighbors visited during vector insert. Lower values make the insert query faster while reducing DiskANN graph navigability properties. Default: `70` |

 For `N` rows, a vector index on type `T1` with `max_neighbors=M` and `compress_neighbors=T2` needs approximately `N × (Storage(T1) + M × Storage(T2))` bytes.
