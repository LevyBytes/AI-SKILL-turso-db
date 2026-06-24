# Examples, Guides, Integrations

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: examples; Prerequisites; Examples; Install.

## examples

[tursodatabase](https://docs.turso.tech/tursodatabase)  / ** [libsql-client-ts](https://docs.turso.tech/tursodatabase/libsql-client-ts) ** Public
-  [Notifications](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql-client-ts) Sign in before updating notification preferences
-  [Fork 66](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql-client-ts)
-   [Star  566](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql-client-ts)

### Expand file tree

/

### examples

/Copy path

### Directory actions

### More options

More options

### Directory actions

### More options

More options

### Latest commit

### History

[History](https://docs.turso.tech/tursodatabase/libsql-client-ts/commits/main/packages/libsql-client/examples)History/

### examples

/Copy path

### Folders and files

| Name | Name | Last commit message | Last commit date |
| --- | --- | --- | --- |
| ### parent directory.. |  |  |  |
| example.js | example.js |  |  |
| package.json | package.json |  |  |
| shell.js | shell.js |  |  |
| sync.js | sync.js |  |  |
| sync_offline.js | sync_offline.js |  |  |
| sync_vector.js | sync_vector.js |  |  |

## Prerequisites

(Figure: Astro banner)

### Prerequisites

 For the best use of this guide, have these pieces ready:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have an Astro project - [learn more](https://docs.astro.build/en/install/auto/#1-run-the-setup-wizard)
 1

Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

2

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

3

Configure libSQL client

src/turso.ts

```
import { createClient } from "@libsql/client/web";

export const turso = createClient({
  url: import.meta.env.TURSO_DATABASE_URL!,
  authToken: import.meta.env.TURSO_AUTH_TOKEN,
});

```

Astro is preparing a new ENV API. [Take a look](https://docs.astro.build/en/reference/configuration-reference/#experimentalenv).4

Execute SQL

```
---
import { turso } from './turso'

const { rows } = await turso.execute('SELECT * FROM table_name')
---

```

### Examples

### Blog

See the full source code

## Prerequisites

(Figure: Elysia banner)

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have an Elysia app - [learn more](https://elysiajs.com/quick-start.html)
 1

Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

2

Retrieve database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

3

Configure libSQL client

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

4

Execute SQL

```
import { Elysia } from "elysia";
import { turso } from "./lib/turso";

const app = new Elysia().get("/items", async () => {
  const { rows } = await turso.execute("SELECT * FROM items");
  return rows;
});

```

### Examples

### Expenses tracker app with Elysia & Turso

See the full source code

## Prerequisites

(Figure: Hono banner)

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Hono app - [learn more](https://hono.dev/top#quick-start)
 1

Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

2

Retrieve database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

3

Configure libSQL client

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

4

Execute SQL

```
import { Hono } from "hono";
import { turso } from "./lib/turso";

const app = new Hono();

app.get("/items", async (c) => {
  const { rows } = await turso.execute("SELECT * FROM items");

  return c.json({ rows });
});

```

### Examples

### Expenses tracker app with Hono & Turso

See the full source code

## Prerequisites

(Figure: Next.js banner)

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Next.js app - [learn more](https://nextjs.org/docs/getting-started/installation)
 1

Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

2

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

3

Configure libSQL client

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

4

Execute SQL

```
import { turso } from "@/lib/turso";

export default async function Page() {
  const { rows } = await turso.execute("SELECT * FROM table_name");

  return (
    <ul>
      {rows.map((row) => (
        <li key={row.id}>{row.id}</li>
      ))}
    </ul>
  );
}

```

### Examples

### Full Stack App

Create an app using Next.js, Turso, and Drizzle ORM.

## Examples

(Figure: Nuxt banner) Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Nuxt app - [learn more](https://nuxt.com/docs/getting-started/installation#new-project)
 1

Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

2

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
NUXT_TURSO_DATABASE_URL=
NUXT_TURSO_AUTH_TOKEN=

```

3

Configure variables inside Nuxt's runtime config.

nuxt.config.ts

```
export default defineNuxtConfig({
  runtimeConfig: {
    turso: {
      databaseUrl: "",
      authToken: "",
    },
  },
});

```

Check that the keys in `runtimeConfig` use the same names as your environment variables. Read more about this [here](https://nuxt.com/docs/guide/going-further/runtime-config).4

Configure libSQL Client.

server/utils/turso.ts

```
import { createClient } from "@libsql/client";
// You can optionally pass in the event to useRuntimeConfig
// import { H3Event } from "h3";

export function useTurso(/* event: H3Event */) {
  const { turso } = useRuntimeConfig(/* event */);

  return createClient({
    url: turso.databaseUrl,
    authToken: turso.authToken,
  });
}

```

5

Execute SQL

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

## Prerequisites

(Figure: Quasar banner)

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Quasar app - [learn more](https://quasar.dev/start/quick-start#step-1-create-a-project)
 1

Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

2

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env.local`.

```
VITE_TURSO_DATABASE_URL="..."
VITE_TURSO_AUTH_TOKEN="..."

```

3

Configure libSQL Client

```
import { createClient } from "@libsql/client/web";

const turso = createClient({
  url: import.meta.env.VITE_TURSO_DATABASE_URL,
  authToken: import.meta.env.VITE_TURSO_AUTH_TOKEN,
});

```

Prevent a [gotcha moment](https://github.com/quasarframework/quasar/discussions/16071) by updating the app configuration with the settings shown below.quasar.config.js

```
{
  build: {
    target: {
      browser: [
        'es2020', 'edge88', 'firefox78', 'chrome87', 'safari13.1'
      ],
      node: 'node16'
    },
    extendViteConf(config) {
      config.optimizeDeps = {
        esbuildOptions: {
          target: 'es2020',
        }
      }
    }
  }
}

```

4

Fetch data from Turso.

IndexPage.vue

```
import { ref } from "vue";

const items = ref();

const { rows } = await turso.execute("select * from my-table");

items.value = rows;

```

### Examples

### Todo App

See the full source code

## Prerequisites

(Figure: Qwik banner)

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Qwik app - [learn more](https://qwik.builder.io/docs/getting-started/#create-an-app-using-the-cli)
 1

Add Turso Integration

```
npm run qwik add turso

```

2

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env.local`.

```
PRIVATE_TURSO_DATABASE_URL="..."
PRIVATE_TURSO_AUTH_TOKEN="..."

```

3

Execute SQL

```
import { tursoClient } from "~/utils/turso";

export const useFrameworks = routeLoader$(
  async (requestEvent: RequestEventBase) => {
    const db = tursoClient(requestEvent["env"]);
    const { rows } = await db.execute("select * from table_name");

    return {
      items: rows,
    };
  },
);

```

### Examples

### Social Website

See the full source code

### Shopping Cart

See the full source code

## Prerequisites

(Figure: Remix banner)

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Remix app - [learn more](https://remix.run/docs/en/main/start/quickstart#quick-start)
 1

Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

2

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

3

Configure libSQL Client.

```
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
});

```

4

Execute SQL

app/routes/_index.ts

```
import type { LoaderFunction } from "@remix-run/node";

import { turso } from "~/lib/turso";

export const loader: LoaderFunction = async () => {
  const { rows } = await turso.execute("SELECT * from TABLE_NAME");

  return {
    items: rows,
  };
};

```

### Examples

### E-commerce Store

See the full source code

### CRM App

See the full source code

## Prerequisites

(Figure: SvelteKit banner)

### Prerequisites

 Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a SvelteKit app - [learn more](https://kit.svelte.dev/docs/creating-a-project)
 1

Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

2

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

3

Configure libSQL Client.

```
import { TURSO_DATABASE_URL, TURSO_AUTH_TOKEN } from "$env/static/private";
import { createClient } from "@libsql/client";

export const turso = createClient({
  url: TURSO_DATABASE_URL,
  authToken: TURSO_AUTH_TOKEN,
});

```

4

Execute SQL

```
import { turso } from "$lib/turso.server";

export async function load() {
  const { rows } = await turso.execute("SELECT * FROM table_name");

  return { rows };
}

```

### Examples

### Blog

See the full source code

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
