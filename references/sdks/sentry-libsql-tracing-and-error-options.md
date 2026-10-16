# Sentry libSQL tracing and error options

[Context index](typescript-framework-workflows.md)

## Install

The [Sentry](https://sentry.io/integrations/turso) integration for `@libsql/client` adds tracing, breadcrumbs, and SQL query error handling. It is in technical preview right now. [Join us in Discord](https://discord.gg/turso) to report issues.

### Install

```
npm install sentry-integration-libsql-client

```

### Quickstart

 After you have a `@libsql/client` instance, connect it to Sentry this way:

```
import * as Sentry from "@sentry/node";
import { createClient } from "@libsql/client";
import { libsqlIntegration } from "sentry-integration-libsql-client";

const libsqlClient = createClient({
  url: "libsql://...",
  authToken: "...",
});

Sentry.init({
  dsn: "...",
  integrations: [libsqlIntegration(libsqlClient, Sentry)],
});

await libsqlClient.execute("SELECT * FROM users");

```

### Options

 Everything is enabled by default. To choose specific behavior, pass a third argument to `libsqlIntegration`.

| Option | Default | Description |
| --- | --- | --- |
| `tracing` | `true` | Enable tracing for SQL queries. |
| `breadcrumbs` | `true` | Enable breadcrumbs for SQL queries. |
| `errors` | `true` | Turn on SQL query error handling. |

```
Sentry.init({
  dsn: "...",
  integrations: [
    libsqlIntegration(libsqlClient, Sentry, {
      tracing: false,
      breadcrumbs: false,
      errors: false,
    }),
  ],
});

```
