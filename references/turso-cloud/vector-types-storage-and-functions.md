# Vector types storage and functions

[Context index](vectors-attach-branches-and-edge-replicas.md) · [Continue](vector-insertion-and-distance-queries.md)

## Vectors

Vector search in Turso and libSQL is available without adding an extension.

### How it works

- Define a table containing one or more vector columns, such as `FLOAT32`
- Supply vector values as binary data, or use the appropriate conversion function, such as `vector32(...)`, to turn text-form vectors into binary.
- Use dedicated vector functions, such as `vector_distance_cos`, to compute similarity for table vectors or vectors supplied by the query.
- Add a special vector index for faster nearest neighbors queries by using the `libsql_vector_idx(column)` expression inside the `CREATE INDEX` statement.
- Search that index through the special `vector_top_k(idx_name, q_vector, k)` [table-valued function](https://www.sqlite.org/vtab.html#table_valued_functions).

### Vectors

### Types

 LibSQL stores vector columns with SQLite's native BLOB [storage class](https://www.sqlite.org/datatype3.html#storage_classes_and_datatypes). To fit SQLite [affinity rules](https://www.sqlite.org/datatype3.html#determination_of_column_affinity), each type name is provided in two forms: a shorter easy-to-enter name and a second name ending in `_BLOB` that follows those affinity rules. Library authors are encouraged to choose the `_BLOB` variants so query results remain generic and broadly usable. Application code may use either form, because SQLite and outside extensions treat the type name only as a **hint**. Since LibSQL adds no new storage class, the `BLOB` value also carries all vector metadata. That adds a few bytes to each row, but it keeps the feature design much simpler. The table below shows the six vector types LibSQL supports today. They are ordered from higher precision with larger storage needs to smaller representations with reduced precision; storage estimates use D as the dimension count for one vector.

| Type names | Storage (bytes) | Description |
| --- | --- | --- |
| `FLOAT64`, `F64_BLOB` | `8D + 1` | IEEE 754 64-bit floating point |
| `FLOAT32`, `F32_BLOB` | `4D` | IEEE 754 32-bit floating point |
| `FLOAT16`, `F16_BLOB` | `2D + 1` | IEEE 754 half precision |
| `FLOATB16`, `FB16_BLOB` | `2D + 1` | bfloat16 floating point |
| `FLOAT8`, `F8_BLOB` | `D + 14` | Each `u8` component `b` reconstructs as `shift + alpha * b` |
| `FLOAT1BIT`, `F1BIT_BLOB` | `ceil(D / 8) + 3` | One bit per component, packed into machine words |

 For most use cases, start with `FLOAT32`; if a vector table contains many rows, consider smaller encodings. `FLOAT16` and `FLOATB16` require the same storage, but their speed and accuracy trade-offs differ. In general, `bfloat16` operations run faster while sacrificing precision.

### Functions

 LibSQL includes several vector-domain functions for vector work. These functions accept binary vectors matching the six types above, or text vectors encoded as a single JSON array of numbers. Current LibSQL function support is listed below:

| Function name | Description |
| --- | --- |
| `vector64`, `vector32`, `vector16`, `vectorb16`, `vector8`, `vector1bit` | Conversion function that takes a valid vector and changes it to the matching target type |
| `vector` | Alias for `vector32` conversion function |
| `vector_extract` | Extraction function that takes a valid vector and returns its text form |
| `vector_distance_cos` | Cosine distance, computed as 1 - cosine similarity, for vectors with the **same type** and **same dimensionality** |
| `vector_distance_l2` | Euclidean distance for vectors with the **same type** and **same dimensionality** |
