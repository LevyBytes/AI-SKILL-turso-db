# Akamai embedded replica deployment

[Context index](replica-hosting-and-recovery-workflows.md)

## Prerequisites

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have an Akamai account; [create one](https://login.linode.com/signup) if needed
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

### Step 2: Fork one of the GitHub embedded replica projects listed below

### [My Expenses Tracker - (Elysia + Bun)](https://github.com/tursodatabase/embedded-replicas-with-js)

See the full source code

### [Web Traffic API - (Rust)](https://github.com/tursodatabase/embedded-replicas-with-rust)

See the full source code

### Step 3: Set up a Linode server

Configure and create a new linode, then [set up SSH authentication](https://www.linode.com/docs/guides/use-public-key-authentication-with-ssh/) so your terminal can securely access the Linode server. Connect to the newly created linode server and prepare the environment for Rust/JavaScript development based on the project forked earlier. Also install and configure [Git](https://git-scm.com/).

### Step 4: Transfer project to Linode server

Use SSH to access the server, clone the GitHub project, and complete the setup steps from its README.

### Step 5: Deploy

Build and run the project, then configure load balancing for it. [pm2](https://www.npmjs.com/package/pm2) is a strong option because it includes load balancing, log monitoring, and bug/exception alerts. Choose your preferred domain provider, reverse proxy approach, and SSL certificate setup. [Caddy](https://caddyserver.com/) is another suitable choice.
