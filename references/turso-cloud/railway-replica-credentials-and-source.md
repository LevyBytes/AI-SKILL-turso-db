# Railway replica credentials and source

[Context index](replica-hosting-and-recovery-workflows.md) · [Continue](railway-replica-service-and-deployment.md)

## Prerequisites

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have the [Railway CLI](https://docs.railway.app/guides/cli#installing-the-cli) installed
### Step 1: Retrieve database credentials

An existing database is required to continue. If one is not available, [create one](https://docs.turso.tech/quickstart). Then get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into the environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Store these values as environment variables.

### Step 2: Get application code

Fork and locally clone this embedded replica project from GitHub:

### [My Expenses Tracker - (Elysia + Bun)](https://github.com/tursodatabase/embedded-replicas-with-js)

See the full source code
