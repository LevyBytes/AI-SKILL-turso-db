# API quickstart authentication and locations

[Context index](api-quickstart-tokens-and-user.md) · [Continue](api-quickstart-group-and-database-creation.md)

## TypeScript

**Using TypeScript?** Rather than working through raw HTTP samples, you can use the [`@tursodatabase/api`](https://www.npmjs.com/package/@tursodatabase/api) client. For setup, see the [SDK quickstart](https://docs.turso.tech/api-reference/introduction#quickstart-with-the-typescript-sdk).

### Step 1: Sign up or log in with the Turso CLI

If you have not done so yet, [install the Turso CLI](https://docs.turso.tech/cli/installation).

```
turso auth signup

```

### Step 2: Find your account or organization slug

The Platform API works with either a personal account or an organization. Use the Turso CLI to find the `slug` for that account or organization:

```
turso org list

```

### Step 3: Generate a new Platform API Token

Next, use the Turso CLI to create a new API Token:

```
turso auth api-tokens mint quickstart --org my-org

```

To limit the token to one organization, add `--org`:

```
turso auth api-tokens mint quickstart --org my-org

```

Store the token in a secure place; it is needed in the next step.

### Step 4: Fetch available Locations

To create a group or database in a chosen region, first request the available region list:

```
curl -L 'https://api.turso.tech/v1/locations' \
  -H 'Authorization: Bearer TOKEN'

```
