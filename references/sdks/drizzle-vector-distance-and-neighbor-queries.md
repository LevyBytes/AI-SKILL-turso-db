# Drizzle vector distance and neighbor queries

[Context index](typescript-orm-and-client-quickstarts.md) · [Setup and preceding context](drizzle-installation-and-schema.md) · [Previous phase](drizzle-vector-type-storage-and-index.md)

### Step 5: Query vector data

Calculate vector distance:

```
const res = await db
  .select({
    distance: sql<number>`vector_distance_cos(${vectorTable.vector}, vector32(${JSON.stringify([2.2, 3.3, 4.4])}))`,
  })
  .from(vectorTable);

console.log(res);

```

Perform efficient nearest neighbor search:

```typescript
const queryVector = JSON.stringify([2.2, 3.3, 4.4]);
const topK = await db
  .select({
    id: vectorTable.id,
    distance: sql<number>`vector_distance_cos(${vectorTable.vector}, vector32(${queryVector}))`,
  })
  .from(sql`vector_top_k('vector_index', vector32(${queryVector}), 5) AS neighbors`)
  .innerJoin(vectorTable, sql`${vectorTable.id} = neighbors.id`);

console.log(topK);
```

 For performant vector work, add indexes that match the query patterns and set dimensions to fit the application.
