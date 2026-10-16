# Vector storage and similarity examples

[Context index](vector-and-window-function-reference.md) · [Setup and preceding context](vector-float-creation-functions.md) · [Previous phase](vector-concatenation-and-slicing.md) · [Continue](vector-data-conversion-examples.md)

### Creating a table with vector embeddings

```
CREATE TABLE articles (
    id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    content TEXT,
    embedding BLOB
);

INSERT INTO articles VALUES (
    1,
    'Introduction to Machine Learning',
    'Machine learning is a subset of artificial intelligence...',
    vector32('[0.12, -0.34, 0.56, 0.78, -0.11, 0.45, -0.23, 0.67]')
);

INSERT INTO articles VALUES (
    2,
    'Database Design Patterns',
    'Relational databases organize data into tables...',
    vector32('[0.82, 0.15, -0.44, 0.23, 0.61, -0.33, 0.09, 0.77]')
);

INSERT INTO articles VALUES (
    3,
    'Neural Networks Explained',
    'Neural networks are computing systems inspired by biological neural networks...',
    vector32('[0.14, -0.28, 0.62, 0.71, -0.09, 0.51, -0.18, 0.59]')
);

```

### Similarity search with ORDER BY distance

```
-- Find articles most similar to a query embedding using cosine distance
SELECT
    id,
    title,
    vector_distance_cos(embedding, vector32('[0.1, -0.3, 0.5, 0.8, -0.1, 0.4, -0.2, 0.6]')) AS distance
FROM articles
ORDER BY distance ASC
LIMIT 5;

```

### Cosine distance vs. L2 distance

 Cosine distance compares vector direction and disregards magnitude. L2 distance compares actual spatial separation.

```
-- Cosine distance: direction matters, not magnitude
SELECT vector_distance_cos(
    vector32('[1.0, 0.0]'),
    vector32('[100.0, 0.0]')
);
-- 0.0 (same direction, cosine distance is 0)

-- L2 distance: magnitude matters
SELECT vector_distance_l2(
    vector32('[1.0, 0.0]'),
    vector32('[100.0, 0.0]')
);
-- 99.0 (very far apart in absolute terms)

```

 Choose cosine distance when vector direction is what matters, such as semantic similarity of text embeddings. Choose L2 distance when exact position is important, such as geospatial coordinates or image feature vectors.
