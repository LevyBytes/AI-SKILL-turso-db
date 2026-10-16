# Quasar database server configuration

[Context index](typescript-framework-workflows.md) · [Continue](quasar-frontend-data-request.md)

Use a Quasar application with a backend that can run `@libsql/client`. The browser requests data from that backend; database credentials remain in its process environment.

### Install and Configure

Install the SDK in the **backend** package:

```sh
npm install @libsql/client
```

Use the Turso CLI to obtain the database URL and a suitably scoped token:

```sh
turso db show --url <database-name>
turso db tokens create <database-name> --read-only
```

Configure these values in the backend environment:

```dotenv
TURSO_DATABASE_URL=libsql://your-database.turso.io
TURSO_AUTH_TOKEN=replace-with-your-token
```

Vite exposes `VITE_` variables to browser bundles. Database tokens must not use that prefix or be injected through Quasar's client build configuration. A backend `.env` file requires the backend's own environment loader; it is not automatically loaded by the browser build.

### Query Route

Mount the [server items endpoint](quasar-server-items-endpoint.md) at `GET /api/items` behind your application's authentication and authorization. It reads the `items` table; provide the schema before serving it.

Use the [frontend data request](quasar-frontend-data-request.md) to fetch that same-origin endpoint. In development, route `/api` through the application's backend or a development proxy. In production, configure the reverse proxy to reach the authorized server route.
