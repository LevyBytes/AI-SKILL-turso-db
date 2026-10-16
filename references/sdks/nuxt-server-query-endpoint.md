# Nuxt server query endpoint

[Context index](typescript-framework-workflows.md) · [Setup and preceding context](nuxt-runtime-configuration-and-database-client.md)

### Step 5: Execute SQL

server/api/items.get.ts

```
export default defineEventHandler(async (event) => {
  const client = useTurso(/* event */);
  const { rows } = await client.execute("select * from table_name");

  return {
    data: {
      items: rows,
    },
  };
});

```

### Examples

### Website + App

See the full source code
