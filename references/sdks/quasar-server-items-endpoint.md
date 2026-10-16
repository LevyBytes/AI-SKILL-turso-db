# Quasar server items endpoint

This complete Node.js handler module runs in the backend. Mount its returned handler at `GET /api/items`; pass your application's `authorize(request)` function, which must authenticate the caller and decide whether that caller may read these items. Do not bundle this module into Quasar's frontend.

```javascript
import { createClient } from "@libsql/client";

export function createItemsHandler(authorize) {
  if (typeof authorize !== "function") throw new TypeError("Authorization is required");
  const client = createClient({
    url: process.env.TURSO_DATABASE_URL,
    authToken: process.env.TURSO_AUTH_TOKEN,
  });
  const handler = async (request, response) => {
    response.setHeader("Content-Type", "application/json");
    response.setHeader("Cache-Control", "no-store");
    if (request.method !== "GET") {
      response.writeHead(405, { Allow: "GET" });
      response.end(JSON.stringify({ error: "Method not allowed" }));
      return;
    }
    try {
      if (!(await authorize(request))) {
        response.writeHead(403);
        response.end(JSON.stringify({ error: "Access denied" }));
        return;
      }
      const { rows } = await client.execute("SELECT * FROM items");
      const body = JSON.stringify({ items: rows },
        (_, value) => typeof value === "bigint" ? value.toString() : value);
      response.writeHead(200);
      response.end(body);
    } catch {
      response.writeHead(500);
      response.end(JSON.stringify({ error: "Unable to read items" }));
    }
  };
  return { handler, close: () => client.close() };
}
```

Call the returned `close()` during backend shutdown after active requests finish. The example authorizes access to the whole `items` result; applications with per-user rows must add their own parameterized ownership filter before serving them.
