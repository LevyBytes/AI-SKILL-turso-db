# Ai And Embeddings, Attach Database, Branching, Data Edge

> Turso Cloud reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Vectors; How it works.

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

 LibSQL stores vector columns with SQLite's native BLOB [storage class](https://www.sqlite.org/datatype3.html#storage_classes_and_datatypes). To fit SQLite [affinity rules](https://www.sqlite.org/datatype3.html#determination_of_column_affinity), each type name is provided in two forms: a shorter easy-to-enter name and a second name ending in `_BLOB` that follows those affinity rules. Library authors are encouraged to choose the `_BLOB` variants so query results remain generic and broadly usable. Application code may use either form, because SQLite and outside extensions treat the type name only as a **hint**. Since LibSQL adds no new storage class, the `BLOB` value also carries all vector metadata. That adds a few bytes to each row, but it keeps the feature design much simpler. The table below shows the six vector types LibSQL supports today. They are ordered from higher precision with larger storage needs to smaller representations with reduced precision; storage estimates use DDD as the dimension count for one vector.
| Type name | Storage (bytes) | Description |
| --- | --- | --- |
| `FLOAT64` | `F64_BLOB` | 8D+18D + 18D+1 | IEEE 754 double precision implemented for 64-bit floating point numbers |
| `FLOAT32` | `F32_BLOB` | 4D4D4D | IEEE 754 single precision implemented for 32-bit floating point numbers |
| `FLOAT16` | `F16_BLOB` | 2D+12D + 12D+1 | IEEE 754-2008 half precision implemented for 16-bit floating point numbers |
| `FLOATB16` | `FB16_BLOB` | 2D+12D + 12D+1 | bfloat16 format implemented for 16-bit floating point numbers |
| `FLOAT8` | `F8_BLOB` | D+14D + 14D+14 | A LibSQL-specific representation that stores each vector component as one `u8` byte `b` and restores the value through the transformation shift+alpha⋅b\texttt{shift} + \texttt{alpha} \cdot bshift+alpha⋅b |
| `FLOAT1BIT` | `F1BIT_BLOB` | ⌈D8⌉+3\lceil \frac{D}{8} \rceil + 3⌈8D⌉+3 | A LibSQL-specific representation that compresses every vector component to 1-bit, packs several components into one machine word, and yields a highly compact encoding |

 For most use cases, start with `FLOAT32`; if a vector table contains many rows, consider smaller encodings. `FLOAT16` and `FLOATB16` require the same storage, but their speed and accuracy trade-offs differ. In general, `bfloat16` operations run faster while sacrificing precision.

### Functions

 LibSQL includes several vector-domain functions for vector work. These functions accept binary vectors matching the six types above, or text vectors encoded as a single JSON array of numbers. Current LibSQL function support is listed below:
| Function name | Description |
| --- | --- |
| `vector64` | `vector32` | `vector16` | `vectorb16` | `vector8` | `vector1bit` | Conversion function that takes a valid vector and changes it to the matching target type |
| `vector` | Alias for `vector32` conversion function |
| `vector_extract` | Extraction function that takes a valid vector and returns its text form |
| `vector_distance_cos` | Cosine distance, computed as 1 - cosine similarity, for vectors with the **same type** and **same dimensionality** |
| `vector_distance_l2` | Euclidean distance for vectors with the **same type** and **same dimensionality** |

### Vectors usage

 1

Create a table

Start by declaring the vector-storage column with datatype `F32_BLOB`:

```
CREATE TABLE movies (
  title     TEXT,
  year      INT,
  embedding F32_BLOB(4) -- 4-dimensional f32 vector
);

```

The `(4)` value sets vector dimensionality, so every vector stored in this column has exactly 4 components.2

Generate and insert embeddings

After creating embeddings for the data through an LLM, insert those values into the table:

```
INSERT INTO movies (title, year, embedding)
VALUES
  ('Napoleon', 2023, vector32('[0.800, 0.579, 0.481, 0.229]')),
  ('Black Hawk Down', 2001, vector32('[0.406, 0.027, 0.378, 0.056]')),
  ('Gladiator', 2000, vector32('[0.698, 0.140, 0.073, 0.125]')),
  ('Blade Runner', 1982, vector32('[0.379, 0.637, 0.011, 0.647]'));

```

Embeddings can be generated with popular tools including [LangChain](https://www.langchain.com), [Hugging Face](https://huggingface.co), or [OpenAI](https://turso.tech/blog/how-to-generate-and-store-openai-vector-embeddings-with-turso).3

Perform a vector similarity search

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
- Cosine Distance = 1 — [Cosine Similarity](https://en.wikipedia.org/wiki/Cosine_similarity)
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
| `metric` | `cosine` | `l2` | Distance function used when building the index. Default: `cosine` |
| `max_neighbors` | positive integer | Number of neighbors kept for each node in the DiskANN graph. Smaller values make the index use less storage but lower search precision. Default: 3D3 \sqrt{D}3D where DDD is the vector column dimensionality |
| `compress_neighbors` | `float1bit`|`float8`| `float16`|`floatb16`| `float32` | Vector type used to store neighbors for each node in the DiskANN graph. More compact neighbor types lower index storage while reducing search precision. Default: **no compression** (neighbors has same type as base table) |
| `alpha` | positive float ≥1\geq 1≥1 | The “Density” parameter for the general sparse neighborhood graph built during DiskANN. Lower values make the DiskANN graph sparser, which can improve query speed while reducing search precision. Default: `1.2` |
| `search_l` | positive integer | Limit on the number of neighbors visited during vector search. Lower values make the search query faster while reducing search precision. Default: `200` |
| `insert_l` | positive integer | Limit on the number of neighbors visited during vector insert. Lower values make the insert query faster while reducing DiskANN graph navigability properties. Default: `70` |

 For `N` rows, a vector index on type `T1` with `max_neighbors=M` and `compress_neighbors=T2` needs about N(Storage(T1)+M⋅Storage(T2)) bytes, equivalent to \texttt{N} (Storage(\texttt {T1}) + \texttt{M} \cdot Storage(\texttt{T2})) or N(Storage(T1)+M⋅Storage(T2)).

### Index usage

 1

Create a table

Start by declaring the vector-storage column with datatype `F32_BLOB`:

```
CREATE TABLE movies (
  title     TEXT,
  year      INT,
  embedding F32_BLOB(4) -- 4-dimensional f32 vector
);

```

The `(4)` value sets vector dimensionality, so every vector stored in this column has exactly 4 components.2

Generate and insert embeddings

After creating embeddings for the data through an LLM, insert those values into the table:

```
INSERT INTO movies (title, year, embedding)
VALUES
  ('Napoleon', 2023, vector32('[0.800, 0.579, 0.481, 0.229]')),
  ('Black Hawk Down', 2001, vector32('[0.406, 0.027, 0.378, 0.056]')),
  ('Gladiator', 2000, vector32('[0.698, 0.140, 0.073, 0.125]')),
  ('Blade Runner', 1982, vector32('[0.379, 0.637, 0.011, 0.647]'));

```

Embeddings can be generated with popular tools including [LangChain](https://www.langchain.com), [Hugging Face](https://huggingface.co), or [OpenAI](https://turso.tech/blog/how-to-generate-and-store-openai-vector-embeddings-with-turso).3

Create an Index

Create an index with the `libsql_vector_idx` function:

```
CREATE INDEX movies_idx ON movies(libsql_vector_idx(embedding));

```

This produces an index optimized for similarity searches over vectors stored in the `embedding` column. The `libsql_vector_idx` marker function is **required**; libSQL uses it to tell `ANN`-indices apart from ordinary B-Tree indices.4

Query the indexed table

```
SELECT title, year
FROM vector_top_k('movies_idx', vector32('[0.064, 0.777, 0.661, 0.687]'), 3)
JOIN movies ON movies.rowid = id
WHERE year >= 2020;

```

This query uses the `vector_top_k` [table-valued function](https://www.sqlite.org/vtab.html#table_valued_functions) and the index to efficiently locate the top 3 vectors most similar to `[0.064, 0.777, 0.661, 0.687]`.

### Index limitations

- Vector indexes work only for tables **with** `ROWID` or with a singular `PRIMARY KEY`; a composite `PRIMARY KEY` without `ROWID` is not supported

## How it works

This feature is deprecated for all new users; existing paid users may still use `ATTACH` - [read the announcement](https://turso.tech/blog/upcoming-changes-to-the-turso-platform-and-roadmap). `ATTACH` lets one transaction link multiple databases, making it useful for:
- Keeping data organized modularly
- Improving scalability while making data access more streamlined
- Aggregating data

### How it works

1. Enable `ATTACH` on the databases that should connect to each other.
2. Get the **Database ID** for the database you plan to `ATTACH`.
3. You connect to the database
  - **CLI**: Use `--attach` so the generated token automatically receives the required permissions.
  - **SDK**: Create a token for the target database with the `attach` permission.

4. Run `ATTACH` from the database shell or SDK to connect the other databases.

### Usage

 Use `ATTACH` to connect other databases inside a transaction from the CLI or a libSQL SDK. After attaching, query each database by its alias as though it belongs to the current database.

### Turso CLI

 Confirm that the [Turso CLI](https://docs.turso.tech/cli/installation) is installed and that you are [logged in](https://docs.turso.tech/cli/auth/login). 1

Enable attach on required databases

First enable `ATTACH` on the database(s) you intend to attach:

```
turso db config attach allow <database-name>

```

2

Retrieve Database ID

Next retrieve the **Database ID** for the database you intend to `ATTACH`:

```
turso db show <database-name>

```

3

Open a connection to any database with attach

When connecting to your database(s), provide the database names through the `--attach` flag:

```
turso db shell <database-name> --attach <...database-name(s)>

```

4

ATTACH

After the connection is open, invoke an `ATTACH` statement to connect the other database(s):

```
ATTACH "<database-id>" AS my_db;

```

5

Query

Run a query through the alias assigned to any attached database(s):

```
SELECT * FROM my_db.my_table;

```

### libSQL SDKs

 Use a libSQL client SDK through [TypeScript](https://docs.turso.tech/sdk/ts), [Rust](https://docs.turso.tech/sdk/rust), [Go](https://docs.turso.tech/sdk/go), [Python](https://docs.turso.tech/sdk/python), or [HTTP](https://docs.turso.tech/sdk/http). 1

Enable attach on required databases

First enable `ATTACH` on the database(s) you intend to attach:

```
turso db config attach allow <database-name>

```

2

Retrieve Database ID

Next retrieve the **Database ID** for the database you intend to `ATTACH`:

```
turso db show <database-name>

```

3

Create token with ATTACH permissions

Then create a libSQL client token with `attach` permission for the database you want to attach:

```
turso db tokens create <database-name> --attach <another-database-name>

```

4

Connect to any database with ATTACH

Attach the desired database inside a read transaction using a [Client SDK](https://docs.turso.tech/sdk):

```
import { createClient } from "@libsql/client";

const client = createClient({
  syncUrl: "libsql://...",
  authToken: "...",
});

const txn = await db.transaction("read");

await txn.execute('ATTACH "<database-id>" AS my_db');

const rs = await txn.execute("SELECT * FROM my_db.my_table");

```

### Things to know

- Only databases with the `attach` feature enabled can be attached.
- The attached databases must belong to a group and share that same group.
- A single transaction can attach at most 10 databases.
- Attached databases are read only.
- Use `ATTACH` statements only inside transactions.
- `ATTACH` doesn’t support [Embedded Replicas](https://docs.turso.tech/features/embedded-replicas)

## How it works

A branch is an independent database instance created from an existing database. You may also create one from a database [point-in-time](https://docs.turso.tech/features/point-in-time-recovery) snapshot. Branches help development and testing because changes can be made without impacting the original database.

### How it works

1. Create a new database from an existing database through the CLI or API.
2. Connect to that new database with the group API token.
3. Optionally use a migration tool to change the new schema.
4. When merging through a GitHub Action, optionally use a migration tool to apply the changes to the original database.
5. Delete the database after it is no longer needed.

### Usage

 Create a new database from an existing database with either the CLI or API:

```
turso db create my-new-database-branch --from-db my-existing-database

```

 For details on every argument, see these references:

### CLI Reference

### API Reference

### Things to know

- Database branches are fully separate from the original database, so any schema changes or data must be merged manually with a migration tool.
- To connect to the new database, [create a new token](https://docs.turso.tech/cli/db/tokens/create) or use a group token.
- Delete the database branch manually after it is no longer needed.
- Branches count against your plan’s database quota.

### CI/CD

 Branch automation is useful when each pull request needs a new database, because changes can be tested without affecting the original database. This example shows one possible setup with the [Platform API](https://docs.turso.tech/api-reference/databases/create): .github/workflows/create-database-branch.yml

```
name: Create Database
on: create

jobs:
  triggerAPI:
    runs-on: ubuntu-latest
    steps:
      - name: Generate Branch Name
        id: branch_name
        run: |
          BRANCH_NAME=$(echo "${{ github.ref_name }}" | tr -cd '[:alnum:]' | sed 's/./\L&/g' | cut -c 1-32)
          echo "::set-output name=branch_name::$BRANCH_NAME"
      - name: Create Database
        run: |
          curl -X POST \
          -H "Authorization: Bearer ${{ secrets.API_TOKEN }}" \
          -H "Content-Type: application/json" \
          -d '{"name": "${{ steps.branch_name.outputs.branch_name }}", "group": "default", "seed": {"type": "database", "name": "${{ secrets.DATABASE_NAME }}"} }' \
          "https://api.turso.tech/v1/organizations/${{ secrets.ORGANIZATION_NAME }}/databases"

```

## How it works

This feature is deprecated for all new users; existing users may continue using Edge Replicas on Fly - [read the announcement](https://turso.tech/blog/upcoming-changes-to-the-turso-platform-and-roadmap). For maximum speed, Turso can [embed databases](https://docs.turso.tech/features/embedded-replicas) directly into the application on the same node. That setup removes inter-regional request hops, effectively reducing latency to zero.

### How it works

1. Create a database in a primary location
2. Add extra locations where the data should be replicated
3. Query a single URL, which routes automatically to the closest edge

### Add replica location

 Add locations to a database group with the [Turso CLI](https://docs.turso.tech/cli/group/locations/add) or [Platform API](https://docs.turso.tech/api-reference/groups/add-location):

```
turso group locations add <group-name> <location-code>

```

### Remove replica location

 Remove locations from a database group with the [Turso CLI](https://docs.turso.tech/cli/group/locations/remove) or [Platform API](https://docs.turso.tech/api-reference/groups/remove-location):

```
turso group locations remove <group-name> <...location-codes>

```
