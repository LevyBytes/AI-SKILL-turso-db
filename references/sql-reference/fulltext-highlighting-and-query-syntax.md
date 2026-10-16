# Fulltext highlighting and query syntax

[Context index](full-text-search-reference.md) · [Setup and preceding context](fulltext-index-tokenizers-and-field-weights.md) · [Previous phase](fulltext-match-and-score-functions.md) · [Continue](fulltext-search-query-examples.md)

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
