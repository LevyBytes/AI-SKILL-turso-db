# JWKS provider setup and token usage

[Context index](activerecord-and-authentication-workflows.md)

## Managing JWKS Endpoints

Rather than handling tokens yourself, you can have your authentication provider create JWT tokens with JWKS and reuse your existing auth system, such as Clerk or Auth0. During the Turso Beta, Clerk & Auth0 are the only supported OIDC providers.

### Step 1: Generate JWT Template

Generate a JWT claims template for your auth provider with the Turso CLI:

```
# Full access to a database
turso org jwks template --database <database-name> --scope full-access

# Read-only access to a group
turso org jwks template --group <group-name> --scope read-only

# Fine-grained permissions
turso org jwks template \
  --database <database-name> \
  --permissions all:data_read \
  --permissions comments:data_add

```

Paste the generated template into the JWT configuration for your auth provider.

### Step 2: Add JWKS Endpoint to Turso

Add your auth provider's JWKS endpoint to your Turso organization:

```
turso org jwks save <name> <url>

```

For example, with Clerk:

```
turso org jwks save clerk https://your-app.clerk.accounts.dev/.well-known/jwks.json

```

JWKS endpoints can also be added from organization settings in the [Turso Dashboard](https://turso.tech/app).

### Step 3: Use Tokens in Your Application

Retrieve the JWT from your auth provider, then supply it through `authToken`:

```
import { connect } from "@tursodatabase/serverless";

const authToken = await getAuthToken(); // e.g., from Clerk, Auth0

const db = connect({
  url: "<your-database-url>",
  authToken,
});

```

### Managing JWKS Endpoints

```
# List all JWKS endpoints
turso org jwks list

# Remove a JWKS endpoint
turso org jwks remove <name>

```

 When no JWT template defines specific permissions, generated tokens default to access for **all databases in all groups**.
