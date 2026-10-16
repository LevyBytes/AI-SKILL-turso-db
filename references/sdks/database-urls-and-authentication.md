# Database urls and authentication

[Context index](activerecord-and-authentication-workflows.md)

## Database URL

SDKs use the `libsql://` protocol when connecting to Turso, except when the [HTTP API](https://docs.turso.tech/sdk/http) is used.

### Database URL

 The database URL is available from the [Turso CLI](https://docs.turso.tech/cli/db/show) or the [Platform API](https://docs.turso.tech/api-reference/databases/retrieve), and it has a form similar to this:

```
libsql://[DB-NAME]-[ORG-NAME].turso.io

```

 For repeated queries over an open connection, WebSockets often perform better; for one-off queries, HTTP may be faster because it can reduce client-server round trips. Benchmark both paths for your workload.

### Auth Tokens

 SDKs need an auth token except when [working locally](https://docs.turso.tech/local-development). Tokens can be created with the [Turso CLI](https://docs.turso.tech/cli) or [Platform API](https://docs.turso.tech/api-reference), or issued by an authentication provider through [JWKS](https://docs.turso.tech/sdk/authorization#jwks-authentication-providers). For token creation, fine-grained permissions, and JWKS configuration, see [Authorization](https://docs.turso.tech/sdk/authorization).
