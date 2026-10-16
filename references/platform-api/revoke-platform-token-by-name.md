# Revoke platform token by name

[Context index](api-quickstart-tokens-and-user.md)

## Authorizations

```
curl -L -X DELETE 'https://api.turso.tech/v1/auth/api-tokens/{tokenName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "token": "..."
}
```

DELETE /v1/auth/api-tokens/{tokenName}

#### Authorizations

**`Authorization`** — string; header; required.

Provide Bearer authentication in the header, using `Bearer <token>` with `<token>` set to your auth token.

#### Path Parameters

**`tokenName`** — string; required.

Name assigned to the api token.

#### Response

200 - application/json

Successful response

**`token`** — string.

The revoked token name.

Example:

`"..."`
