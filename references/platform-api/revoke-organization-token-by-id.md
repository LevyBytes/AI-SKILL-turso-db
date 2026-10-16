# Revoke organization token by ID

[Context index](api-quickstart-tokens-and-user.md)

## Authorizations

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/api-tokens/{tokenId}' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "token": "clGFZ4STEe6fljpFzIum8A"
}
```

DELETE /v1/organizations/{organizationSlug}/api-tokens/{tokenId}

This endpoint requires the token **ID** instead of its name, because multiple users in an organization can create tokens with the same name. Admins and owners can revoke any token scoped to that organization; members and viewers can revoke only tokens they minted. For unrestricted tokens, use [`DELETE /v1/auth/api-tokens/{tokenName}`](https://docs.turso.tech/api-reference/tokens/revoke).

#### Authorizations

**`Authorization`** — string; header; required.

Use the authorization header `Bearer <token>`, where `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug for the organization or user account.

**`tokenId`** — string; required.

Token ID to revoke, taken from the list endpoint.

#### Response

200 - application/json

Successful response

**`token`** — string.

ID of the token that was revoked.

Example:

`"clGFZ4STEe6fljpFzIum8A"`
