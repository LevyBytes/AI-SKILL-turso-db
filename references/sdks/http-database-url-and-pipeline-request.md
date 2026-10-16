# HTTP database url and pipeline request

[Context index](http-api-and-package-selection.md) · [Setup and preceding context](http-client-packages-and-quickstart.md) · [Continue](http-pipeline-response-and-typed-arguments.md)

Create HTTP Database URL

Fetch the database URL with the [Turso CLI](https://docs.turso.tech/cli) or the [Platform API](https://docs.turso.tech/api-reference):

```
turso db show <database-name> --http-url

```

Add `/v2/pipeline` to the URL, then proceed.

### Step 2: Create Database Auth Token

Create a fresh auth token for the database with the Turso CLI or Platform API:

```
turso db tokens create <database-name>

```

### Step 3: Create JSON Request Payload

Because the request body is JSON, build a JSON payload that runs a SQL statement and then closes the connection immediately:

```
{
  "requests": [
    { "type": "execute", "stmt": { "sql": "SELECT * FROM users" } },
    { "type": "close" }
  ]
}

```

Change `stmt.sql` to query a table that already exists. Bound parameter examples appear on the [Reference Page](https://docs.turso.tech/sdk/http/reference#parameter-binding).

### Step 4: Execute HTTP Request

Use an HTTP client library for your language to send the request to the URL you created. Set the `Authorization` header to the token, and send a JSON body containing the SQL statement. Append the pipeline path that accepts requests, `/v2/pipeline`, to the Base URL.

```
const url = "https://[databaseName]-[organizationSlug].turso.io/v2/pipeline";
const authToken = "...";

fetch(url, {
  method: "POST",
  headers: {
    Authorization: `Bearer ${authToken}`,
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    requests: [
      { type: "execute", stmt: { sql: "SELECT * FROM users" } },
      { type: "close" },
    ],
  }),
})
  .then((res) => res.json())
  .then((data) => console.log(data))
  .catch((err) => console.log(err));

```
