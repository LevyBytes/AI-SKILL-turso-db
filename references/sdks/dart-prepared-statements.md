# Dart prepared statements

[Context index](client-connection-and-vercel-workflows.md) · [Setup and preceding context](dart-installation-connection-and-queries.md)

### Step 6: Prepared statements

Use prepared statements when you want safer parameter handling and better repeated-query performance:

```
final statement = await client.prepare("SELECT * FROM customers WHERE id = ?");
final result = await statement.query(positional: [1]);
print(result);

```
