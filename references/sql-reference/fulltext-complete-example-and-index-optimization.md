# Fulltext complete example and index optimization

[Context index](full-text-search-reference.md) · [Setup and preceding context](fulltext-index-tokenizers-and-field-weights.md) · [Previous phase](fulltext-search-query-examples.md) · [Continue](fulltext-updates-fts5-comparison-and-limits.md)

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
