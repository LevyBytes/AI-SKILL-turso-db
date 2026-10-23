# Create platform token request

[Context index](api-quickstart-tokens-and-user.md) · [Continue](create-platform-token-fields.md)

## Authorizations

```
curl -L -X POST https://api.turso.tech/v1/auth/api-tokens/{tokenName} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"organization": "my-org"}'

```

```
{
  "name": "<unknown>",
  "id": "<unknown>",
  "token": "..."
}
```

POST /v1/auth/api-tokens/{tokenName}

The response shows `token` only once. Save it securely, and do not share it or commit it to source control. **A future release will remove unrestricted (cross-org) tokens, which are deprecated.** Include at least `organization` in every request body.

#### Authorizations

**`Authorization`**: string; header; required.

Use a Bearer authentication header in the format `Bearer <token>`; `<token>` is your auth token.

#### Path Parameters

**`tokenName`**: string; required.

Name assigned to the api token.
