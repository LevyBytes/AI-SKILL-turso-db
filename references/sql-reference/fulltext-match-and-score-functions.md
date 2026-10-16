# Fulltext match and score functions

[Context index](full-text-search-reference.md) · [Setup and preceding context](fulltext-index-tokenizers-and-field-weights.md) · [Continue](fulltext-highlighting-and-query-syntax.md)

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

 Calculates the BM25 relevance value for each matched row. Larger scores represent stronger relevance.

```
fts_score(column1, column2, ..., query)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `column1, column2, ...` | TEXT | A column list, with one or more entries, that belongs to an FTS index |
| `query` | TEXT | The search query string |

 **Returns:** REAL - the BM25 relevance score, where larger values are more relevant. Use `ORDER BY score DESC` to put the strongest matches first. In SELECT, the FTS index automatically supplies scored results.

```
SELECT
    id,
    title,
    fts_score(title, body, 'database') AS score
FROM articles
ORDER BY score DESC
LIMIT 10;

```
