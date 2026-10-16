# Render embedded replica deployment

[Context index](replica-hosting-and-recovery-workflows.md)

## Prerequisites

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have a Render account; [create one](https://dashboard.render.com/) if needed
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

Fork this embedded replica project from GitHub locally:

### [My Expenses Tracker - (Elysia + Bun)](https://github.com/tursodatabase/embedded-replicas-with-js)

Review the complete source code

Or, choose the deploy path:

### [Deploy to Render with a single-click](https://render.com/deploy?repo=https://github.com/tursodatabase/embedded-replicas-with-js)

### Step 3: Create a web service

In the Render dashboard, create a new Render **Web Service** by selecting the "New Web Service" button on the Web Services card.

### Step 4: Connect to Git repository

1.  Choose "build and deploy from a Git repository", then continue to the next page.
2.  Select "Connect" for the target project repository.

### Step 5: Set project's environment variables

On the web service configuration page, open "Advanced", add **a secret file**, and enter the database secret credentials. (Figure: Render secret credentials)

### Step 6: Deploy project

Go to the bottom of the web service configuration page and select "Create Web Service".
