# Drizzle vector type storage and index

[Context index](typescript-orm-and-client-quickstarts.md) · [Setup and preceding context](drizzle-installation-and-schema.md) · [Previous phase](drizzle-configuration-migrations-and-queries.md) · [Continue](drizzle-vector-distance-and-neighbor-queries.md)

### Vector Embeddings

 Drizzle can be expanded for Turso native vector support; [learn more](https://docs.turso.tech/features/ai-and-embeddings).

### Step 1: Define custom vector type

Add this content inside `db/schema.ts`:

```
import { sql } from "drizzle-orm";
import { customType } from "drizzle-orm/sqlite-core";

const float32Array = customType<{
  data: number[];
  config: { dimensions: number };
  configRequired: true;
  driverData: Uint8Array | ArrayBuffer;
}>({
  dataType(config) {
    return `F32_BLOB(${config.dimensions})`;
  },
  fromDriver(value: Uint8Array | ArrayBuffer) {
    const bytes = value instanceof ArrayBuffer ? new Uint8Array(value) : value;
    if (bytes.byteLength % 4 !== 0) throw new Error("Invalid FLOAT32 vector bytes");
    const view = new DataView(bytes.buffer, bytes.byteOffset, bytes.byteLength);
    return Array.from({ length: bytes.byteLength / 4 },
      (_, index) => view.getFloat32(index * 4, true));
  },
  toDriver(value: number[]) {
    return sql`vector32(${JSON.stringify(value)})`;
  },
});

```

### Step 2: Define a table that includes a vector column

When declaring the schema, call `float32Array` to define the vector-storage column:

```
import { sqliteTable, integer } from "drizzle-orm/sqlite-core";

export const vectorTable = sqliteTable("vector_table", {
  id: integer("id").primaryKey(),
  vector: float32Array("vector", { dimensions: 3 }),
});

```

### Step 3: Create a vector index

Raw SQL is required for creating the index:

```
import { drizzle } from "drizzle-orm/libsql";
import { createClient } from "@libsql/client";

const client = createClient({
  url: process.env.TURSO_DATABASE_URL!,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

const db = drizzle(client);

await db.run(sql`
  CREATE INDEX IF NOT EXISTS vector_index
  ON vector_table(libsql_vector_idx(vector))
`);

```

### Step 4: Insert vector data

```
await db
  .insert(vectorTable)
  .values([{ vector: sql`vector32(${JSON.stringify([1.1, 2.2, 3.3])})` }]);

```
