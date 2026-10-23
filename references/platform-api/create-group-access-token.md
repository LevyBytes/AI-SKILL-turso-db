# Create group access token

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/auth/tokens?expiration=2w&authorization=full-access' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "jwt": "TOKEN"
}

```

POST /v1/organizations/{organizationSlug}/groups/{groupName}/auth/tokens

After creation, tokens are not retrievable and cannot be revoked one at a time.

#### Authorizations

**`Authorization`**: string; header; required.

Authenticate with a Bearer header written as `Bearer <token>`; replace `<token>` with your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug for the organization or user account.

**`groupName`**: string; required.

The name of the group.

#### Query Parameters

**`expiration`** - string; default: never.

Set when the token expires, for example 2w1d30m.

**`authorization`** - enum&lt;string&gt;; default: full-access.

Authorization level granted to the token: full-access or read-only.

Options supported: `full-access`, `read-only`

#### Body

application/json

Extra context, such as claims the token must include.

**`permissions`**: object.

The permissions for the token.

Open when you need the nested `permissions` contract: [field details](token-attach-permission-fields.md).

#### Response

Successful response

**`jwt`**: string.

The generated authorization token (JWT).
