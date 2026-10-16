# Rocket database query and route

[Context index](rust-framework-and-connection-workflows.md)

## Prerequisites

### Prerequisites

 Before continuing, confirm you have these prerequisites:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation)
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Rocket app - [learn more](https://rocket.rs/v0.5/guide/getting-started/)
### Step 1: Retrieve database credentials

You need an existing database before continuing. If one is not available, [create one](https://docs.turso.tech/quickstart).Get the database URL:

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

Store these values as environment variables.

### Step 2: Add the libsql crate to the project

```
cargo add libsql

```

Optionally add a package such as [`dotenvy`](https://docs.rs/dotenvy/latest/dotenvy) to make `.env` files easier to use:

```
cargo add dotenvy

```

### Step 3: Execute SQL

Open the complete [Rocket query application](rocket-todo-query-application.md) after setup. It declares the route model and imports and uses the asynchronous libSQL Builder/Rows APIs.

### Examples

### Turso + Rocket Todo List

See the full source code
