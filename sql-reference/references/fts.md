# Fts

> Sql Reference reference. Original prose; identifiers preserved verbatim.

### Full-Text Search

 **Turso Extension**: Turso implements full-text search (FTS) with [Tantivy](https://github.com/quickwit-oss/tantivy) rather than SQLite's FTS3/FTS4/FTS5 modules. Its syntax and function behavior are separate from SQLite FTS. Turso exposes this capability through custom FTS indexes and three SQL functions: `fts_match` to filter rows, `fts_score` to rank relevance, and `fts_highlight` to render results with matched terms marked.

---

### Creating FTS Indexes

 Define an FTS index by adding the `USING fts` clause to `CREATE INDEX`. Every indexed column is included in full-text search.

```
CREATE INDEX index_name ON table_name USING fts (column1, column2, ...);

```

### Tokenizer Configuration

 Use the `WITH` clause to choose a tokenizer separately for each column.

```
-- All columns use the default tokenizer
CREATE INDEX idx_articles ON articles USING fts (title, body);

-- Per-column tokenizer configuration
CREATE INDEX idx_articles ON articles USING fts (
    title WITH tokenizer=simple,
    body WITH tokenizer=ngram
);

-- Global tokenizer for all columns
CREATE INDEX idx_tags ON tags USING fts (tag) WITH (tokenizer = 'raw');

```

### Available Tokenizers

| Tokenizer | Description | Use case |
| --- | --- | --- |
| `default` | Tokenizer that handles Unicode, lowercases terms, splits punctuation, and applies a 40-char limit | General-purpose text search |
| `raw` | Keeps the complete field value as one token; no tokenization is applied | IDs, UUIDs, tags, exact-match fields |
| `simple` | Breaks text on whitespace and punctuation, but does not lowercase terms | Simple text without case-insensitive needs |
| `whitespace` | Splits on whitespace only | Whitespace-delimited tokens |
| `ngram` | Builds 2-3 character n-grams from the supplied text | Autocomplete, substring matching |

 **Tokenizer examples:**
| Input | default | raw | ngram |
| --- | --- | --- | --- |
| `Hello World` | `hello`, `world` | `Hello World` | `He`, `Hel`, `el`, `ell`, `ll`, `llo`, … |
| `user-123` | `user`, `123` | `user-123` | `us`, `use`, `se`, `ser`, … |

### Field Weights

 Set relative column weights to adjust each indexed column's effect on the BM25 relevance score.

```
CREATE INDEX idx_articles ON articles USING fts (title, body)
    WITH (weights = 'title=2.0,body=1.0');

```

| Parameter | Default | Description |
| --- | --- | --- |
| `weights` | `1.0` for all fields | Comma-separated `column=weight` pairs. Higher weights increase score contribution. |

---

### Functions

### fts_match

 Produces 1 when the row satisfies the full-text query and 0 when it does not; it is typically used in the `WHERE` clause to limit results.

```
fts_match(column1, column2, ..., query)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `column1, column2, ...` | TEXT | One or more columns included in an FTS index |
| `query` | TEXT | The query string for the search; details are in Query Syntax below |

 **Returns:** INTEGER - 1 for a matching row, otherwise 0. Columns supplied to `fts_match` need to belong to an existing FTS index. If Turso's query planner finds `fts_match` in a WHERE clause, it uses the FTS index for fast lookup.

```
SELECT id, title FROM articles
WHERE fts_match(title, body, 'database');

-- Single column
SELECT id, title FROM articles
WHERE fts_match(body, 'machine learning');

```

### fts_score

 Calculates the BM25 relevance value for each matched row. Smaller scores represent stronger relevance.

```
fts_score(column1, column2, ..., query)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `column1, column2, ...` | TEXT | A column list, with one or more entries, that belongs to an FTS index |
| `query` | TEXT | The search query string |

 **Returns:** REAL - the BM25 relevance score, where smaller values are more relevant. Choose `ORDER BY score ASC` or `ORDER BY score DESC` based on the ordering you want. In SELECT, the FTS index automatically supplies scored results.

```
SELECT
    id,
    title,
    fts_score(title, body, 'database') AS score
FROM articles
ORDER BY score DESC
LIMIT 10;

```

### fts_highlight

 Returns text where query matches are surrounded by caller-provided tags. This is useful for search result displays that emphasize matched terms.

```
fts_highlight(column1, column2, ..., open_tag, close_tag, query)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `column1, column2, ...` | TEXT | One or more text columns that should receive highlighting |
| `open_tag` | TEXT | The opening tag placed before each matched term, for example `'<b>'` |
| `close_tag` | TEXT | The closing tag placed after each matched term, for example `'</b>'` |
| `query` | TEXT | The search query string |

 **Returns:** TEXT - the supplied text after matching terms are enclosed in the specified tags. If there are no matches, the original text is returned. If query, open_tag, or close_tag is NULL, the result is NULL. With multiple columns, their text is joined using spaces.

```
SELECT fts_highlight(title, '<b>', '</b>', 'database') AS highlighted
FROM articles
WHERE fts_match(title, body, 'database');
-- <b>Database</b> Design Patterns

-- Multiple columns are concatenated
SELECT fts_highlight(title, body, '<mark>', '</mark>', 'database') AS highlighted
FROM articles
WHERE fts_match(title, body, 'database');

-- Standalone usage (without an FTS index)
SELECT fts_highlight('The quick brown fox', '<em>', '</em>', 'quick fox');
-- The <em>quick</em> brown <em>fox</em>

```

---

### Query Syntax

 Query strings passed to `fts_match` and `fts_score` follow Tantivy's query parser syntax.

### Basic queries

| Syntax | Example | Description |
| --- | --- | --- |
| Single term | `'database'` | Match rows containing “database” |
| Multiple terms (OR) | `'database search'` | Find rows that contain "database" OR "search" |
| Boolean AND | `'database AND search'` | Match rows containing both terms |
| Boolean NOT | `'database NOT nosql'` | Match “database” but exclude “nosql” |

### Advanced queries

| Syntax | Example | Description |
| --- | --- | --- |
| Phrase search | `'"exact phrase"'` | Match the exact phrase |
| Prefix search | `'data*'` | Match terms starting with “data” |
| Column-specific | `'title:database'` | Match "database" only when it appears in the `title` field |
| Boosting | `'title:database^2'` | Increase matches found in `title` by a factor of 2 |

### Examples

```
-- Simple term search
SELECT * FROM articles WHERE fts_match(title, body, 'database');

-- Phrase search: match "full text search" as an exact phrase
SELECT * FROM articles WHERE fts_match(title, body, '"full text search"');

-- Boolean AND: both terms must be present
SELECT * FROM articles WHERE fts_match(title, body, 'database AND performance');

-- Prefix search: match "optim", "optimize", "optimization", etc.
SELECT * FROM articles WHERE fts_match(title, body, 'optim*');

-- Column-specific: only match "rust" in the title field
SELECT * FROM articles WHERE fts_match(title, body, 'title:rust');

-- Boosted search: title matches count 2x more toward the score
SELECT id, title, fts_score(title, body, 'title:database^2 body:database') AS score
FROM articles
ORDER BY score DESC;

-- Exclusion: match "database" but not "nosql"
SELECT * FROM articles WHERE fts_match(title, body, 'database NOT nosql');

```

---

### Complete Example

 The scenario covers table creation, adding an FTS index, loading data, and querying with both scoring and highlighting.

```
-- Create a table
CREATE TABLE articles (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    body TEXT,
    author TEXT
);

-- Create an FTS index on title and body with field weights
CREATE INDEX idx_articles_fts ON articles USING fts (title, body)
    WITH (weights = 'title=2.0,body=1.0');

-- Insert sample data
INSERT INTO articles VALUES (1, 'Introduction to Databases', 'Databases store and organize data for efficient retrieval.', 'Alice');
INSERT INTO articles VALUES (2, 'Full-Text Search in Practice', 'Full-text search allows finding documents by content.', 'Bob');
INSERT INTO articles VALUES (3, 'Database Performance Tuning', 'Optimizing database queries requires understanding indexes.', 'Alice');
INSERT INTO articles VALUES (4, 'Getting Started with Rust', 'Rust is a systems programming language focused on safety.', 'Carol');

-- Simple search: find articles mentioning "database"
SELECT id, title FROM articles
WHERE fts_match(title, body, 'database');
-- 1 | Introduction to Databases
-- 3 | Database Performance Tuning

-- Ranked search: order by relevance
SELECT
    id,
    title,
    fts_score(title, body, 'database') AS score
FROM articles
WHERE fts_match(title, body, 'database')
ORDER BY score DESC
LIMIT 10;

-- Highlighted results
SELECT
    id,
    fts_highlight(title, '<b>', '</b>', 'database') AS title,
    fts_highlight(body, '<b>', '</b>', 'database') AS body
FROM articles
WHERE fts_match(title, body, 'database');

-- Combine FTS with regular SQL filters
SELECT id, title, author,
    fts_score(title, body, 'database') AS score
FROM articles
WHERE fts_match(title, body, 'database')
  AND author = 'Alice'
ORDER BY score DESC;

```

---

### Index Maintenance

### OPTIMIZE INDEX

 Combines every Tantivy segment into one optimized segment, which can reduce storage overhead and improve query speed, especially after bulk inserts.

```
-- Optimize a specific FTS index
OPTIMIZE INDEX idx_articles_fts;

-- Optimize all FTS indexes in the database
OPTIMIZE INDEX;

```

 **When to use:**
- Following large data loads that use many INSERT statements
- If query speed gets worse over time
- During scheduled maintenance windows
 **What it does:**
- Writes any queued documents out to disk
- Combines all segments into one segment
- Removes deleted document tombstones
- Clears internal caches so later reads use current data

### DML and Index Updates

 FTS indexes stay in sync automatically as the underlying table changes.
| Operation | FTS behavior |
| --- | --- |
| `INSERT` | New rows enter the index right away (commits are batched every 1000 documents) |
| `UPDATE` | Implemented as DELETE + INSERT internally |
| `DELETE` | Deleted documents are recorded with tombstones and cleaned up during OPTIMIZE |

```
-- All of these automatically update the FTS index
INSERT INTO articles VALUES (5, 'New Article', 'Content here.', 'Dave');
UPDATE articles SET body = 'Updated content.' WHERE id = 5;
DELETE FROM articles WHERE id = 5;

```

---

### Comparison with SQLite FTS5

| Feature | SQLite FTS5 | Turso FTS |
| --- | --- | --- |
| Filtering | `WHERE t MATCH 'query'` | `WHERE fts_match(cols, 'query')` |
| Ranking | `bm25(t)`, `rank` column | `fts_score(cols, 'query')` |
| Highlighting | `highlight(t, col_idx, open, close)` | `fts_highlight(cols, open, close, query)` |
| Snippets | `snippet(t, ...)` | Not supported |
| Boolean operators | AND, OR, NOT | AND, OR, NOT |
| Phrase search | `"exact phrase"` | `"exact phrase"` |
| Prefix search | `word*` | `word*` |
| Column filter | `col:term` | `col:term` |
| Tokenizers | unicode61, ascii, porter | default, raw, simple, whitespace, ngram |
| Segment management | Automatic | Manual via `OPTIMIZE INDEX` |
| Transaction visibility | Immediate | After COMMIT |

---

### Current Limitations

- **No snippet function**: Use `fts_highlight` when you need term emphasis; context snippets are not available yet.
- **No automatic segment merging**: Run `OPTIMIZE INDEX` periodically after bulk writes.
- **No read-your-writes in a transaction**: FTS updates made inside a transaction are invisible to queries until the transaction is committed. ROLLBACK still removes both table and FTS changes.
- **No MATCH operator syntax**: Call `fts_match()` instead of writing `WHERE table MATCH 'query'`.

### See Also

- [CREATE INDEX](https://docs.turso.tech/sql-reference/statements/create-index) covers the full `CREATE INDEX ... USING fts` syntax
- [Vector Functions](https://docs.turso.tech/sql-reference/functions/vector) describes similarity search with embeddings
