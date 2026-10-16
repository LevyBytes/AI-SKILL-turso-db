# Retrieve current user

[Context index](api-quickstart-tokens-and-user.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/user \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "user": {
    "username": "iku",
    "name": "Iku Turso",
    "email": "iku@turso.tech",
    "avatarUrl": "https://avatars.githubusercontent.com/u/12345",
    "plan": "scaler",
    "mfa": false,
    "has_pending_invites": false
  }
}
```

GET /v1/user

#### Authorizations

**`Authorization`** — string; header; required.

Send a Bearer auth header in the form `Bearer <token>`, with `<token>` replaced by your auth token.

#### Response

200 - application/json

Successful response

**`user`** — object.

Open when you need the nested `user` contract: [field details](current-user-response-fields.md).
