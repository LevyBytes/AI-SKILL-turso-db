# Fulltext updates fts5 comparison and limits

[Context index](full-text-search-reference.md) · [Setup and preceding context](fulltext-index-tokenizers-and-field-weights.md) · [Previous phase](fulltext-complete-example-and-index-optimization.md)

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
