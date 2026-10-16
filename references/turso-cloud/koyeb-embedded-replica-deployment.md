# Koyeb embedded replica deployment

[Context index](replica-hosting-and-recovery-workflows.md)

## Prerequisites

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have a Koyeb account; [create one](https://app.koyeb.com/) if needed
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

Keep these credentials as environment variables.

### Step 2: Fork one of the GitHub embedded replica project examples listed below

### [My Expenses Tracker - (Elysia + Bun)](https://github.com/tursodatabase/embedded-replicas-with-js)

See the full source code

### [Web Traffic API - (Rust)](https://github.com/tursodatabase/embedded-replicas-with-rust)

Review the complete source code

Or, choose the deploy path:

### [Deploy to Koyeb with a single-click](https://app.koyeb.com/deploy?name=er-with-js&type=git&repository=tursodatabase/embedded-replicas-with-js&branch=main&env[PORT]=8000&env[TURSO_DATABASE_URL]=REPLACE_ME&env[TURSO_AUTH_TOKEN]=REPLACE_ME&env[LOCAL_DB]=file:expenses.db)

### Step 3: Add a new Koyeb app

1.  In the Koyeb control panel, create a new app.
2.  Choose GitHub as the deployment option.
3.  Bring the GitHub project into Koyeb by importing it.

### Step 4: On Koyeb's deploy page, fill in the environment variables.

(Figure: Koyeb deploy page - environment variables)

### Step 5: Deploy

Use the **Deploy** button at the bottom to deploy the web service.
