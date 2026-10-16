# Nuxt runtime configuration and database client

[Context index](typescript-framework-workflows.md) · [Continue](nuxt-server-query-endpoint.md)

## Examples

(Figure: Nuxt banner) Before continuing, confirm you have the following:
- [Install Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Nuxt app - [learn more](https://nuxt.com/docs/getting-started/installation#new-project)
### Step 1: Install the libSQL SDK

Start by adding `@libsql/client` as a dependency in your project:

```
npm install @libsql/client

```

### Step 2: Configure database credentials

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

### Step 3: Configure variables inside Nuxt's runtime config.

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

Check that the keys in `runtimeConfig` use the same names as your environment variables. Read more about this [here](https://nuxt.com/docs/guide/going-further/runtime-config).

### Step 4: Configure libSQL Client.

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
