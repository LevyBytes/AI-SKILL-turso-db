# Fulltext index tokenizers and field weights

[Context index](full-text-search-reference.md) · [Continue](fulltext-match-and-score-functions.md)

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
