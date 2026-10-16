# Fulltext search query examples

[Context index](full-text-search-reference.md) · [Setup and preceding context](fulltext-index-tokenizers-and-field-weights.md) · [Previous phase](fulltext-highlighting-and-query-syntax.md) · [Continue](fulltext-complete-example-and-index-optimization.md)

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
