# Prisma adapter and schema setup

[Context index](typescript-orm-and-client-quickstarts.md) · [Continue](prisma-migrations-and-queries.md)

## Prerequisites

### Prerequisites

 Before continuing, verify that you have completed these steps:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Prisma versions 5.4.2 and later
### Step 1: Add the libSQL SDK along with its Prisma driver

```
npm install @libsql/client @prisma/adapter-libsql

```

### Step 2: Retrieve database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the required credentials into the `.env` environment variables.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

### Step 3: Turn on the `driverAdapters` preview feature flag:

prisma/schema.prisma

```prisma
generator client {
  provider        = "prisma-client-js"
  previewFeatures = ["driverAdapters"]
}

datasource db {
  provider = "sqlite"
  url      = "file:./dev.db"
}

```

### Step 4: Generate Prisma client

```
npx prisma generate

```

### Step 5: Update your Prisma Client Instance

```
import { PrismaClient } from "@prisma/client";
import { PrismaLibSQL } from "@prisma/adapter-libsql";

const adapter = new PrismaLibSQL({
  url: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
})
const prisma = new PrismaClient({ adapter })

```
