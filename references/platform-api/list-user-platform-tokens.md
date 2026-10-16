# List user platform tokens

[Context index](api-quickstart-tokens-and-user.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/auth/api-tokens \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "tokens": [
    {
      "name": "my-token",
      "id": "clGFZ4STEe6fljpFzIum8A",
      "organization": "my-org",
      "group": "default",
      "scopes": [
        "db:create",
        "db:configure",
        "db:mint-token"
      ],
      "created_at": "2026-05-21"
    }
  ]
}
```

GET /v1/auth/api-tokens

#### Authorizations

**`Authorization`** — string; header; required.

Authenticate with a Bearer header formatted as `Bearer <token>`, replacing `<token>` with your auth token.

#### Response

200 - application/json

Successful response

**`tokens`** — object[].

Open when you need the nested `tokens` contract: [field details](token-list-response-fields.md).
