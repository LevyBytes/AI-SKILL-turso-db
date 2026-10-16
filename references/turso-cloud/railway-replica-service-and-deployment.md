# Railway replica service and deployment

[Context index](replica-hosting-and-recovery-workflows.md) · [Setup and preceding context](railway-replica-credentials-and-source.md)

### [Web Traffic API - (Rust)](https://github.com/tursodatabase/embedded-replicas-with-rust)

See the full source code

### Step 3: Create a new Railway project

Create a new Railway project by running the following command, and enter the project's name when prompted.

```
railway init

```

### Step 4: Add a service to the Railway project.

[Create a new empty service](https://docs.railway.app/guides/services#creating-a-service) in the Railway project so it can serve as the deployment target for the app.

### Step 5: Link application to service

List the services and choose the one to link with your application by running this command:

```
railway service

```

### Step 6: Add database credentials

In the Railway dashboard, open the service and add the Turso Cloud database credentials.

```
TURSO_DATABASE_URL=libsql://[db-name]-[github-username].turso.io
TURSO_AUTH_TOKEN=...
LOCAL_DB=file:local-db-name.db

```

### Step 7: Deploy

Deploy the application with this command:

```
railway up

```

Ensure the application is [exposed to the internet](https://docs.railway.app/guides/public-networking) so the public network can reach it. On a free plan, code deployments require connecting the Railway account to GitHub.
