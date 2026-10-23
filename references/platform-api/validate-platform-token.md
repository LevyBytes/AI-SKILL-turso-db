# Validate platform token

[Context index](api-quickstart-tokens-and-user.md)

## Authorizations

```
curl -L 'https://api.turso.tech/v1/auth/validate' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "exp": 999
}
```

GET /v1/auth/validate

#### Authorizations

**`Authorization`**: string; header; required.

Authenticate by sending the header value `Bearer <token>`; `<token>` is your auth token.

#### Response

200 - application/json

Successful response

**`exp`**: integer.

Expiration time for the supplied token, represented as unix epoch seconds; `-1` means it does not expire.

Example:

`999`
