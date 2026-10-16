# Fly embedded replica deployment

[Context index](replica-hosting-and-recovery-workflows.md)

## Prerequisites

### Prerequisites

 Before starting, confirm that you:
- Have the [Turso CLI](https://docs.turso.tech/cli/installation) installed
- Have [signed up or logged in to Turso Cloud](https://docs.turso.tech/cli/authentication#signup)
- Have the [Fly.io CLI](https://fly.io/docs/hands-on/install-flyctl/) installed
### Step 1: Locate your application

You should already have an application that uses the Turso Cloud database you plan to deploy to Fly.

### Step 2: Launch with Fly

Launch it with the Fly CLI:

```
fly launch

```

Fly will deploy the application automatically, but additional setup is still required.

### Step 3: Create a shared volume

Create the volume that will hold the embedded replica(s):

```
fly volumes create libsql_data

```

### Step 4: Mount and configure volumes

The earlier launch created `fly.toml` and `Dockerfile`. Update `fly.toml` so it mounts the new volume:

```
[[mounts]]
source = "libsql_data"
destination = "/app/data"

```

Inside `Dockerfile`, ensure that `ca-certificates` is installed and updated:

```
RUN apt-get update -qq && \
    apt-get install -y ca-certificates && \
    update-ca-certificates

```

Also place the following line after any `COPY` commands so the certificates are copied:

```
COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

```

### Step 5: Configure the libSQL client

Change `url` so it targets a local file, and set `syncUrl` to the Turso Cloud database URL:

```
import { createClient } from "@libsql/client";

const client = createClient({
  url: "file:/app/data/local.db",
  syncUrl: process.env.TURSO_DATABASE_URL,
  authToken: process.env.TURSO_AUTH_TOKEN,
  syncInterval: 60,
});

```

### Step 6: Deploy your updated app

```
fly deploy

```
