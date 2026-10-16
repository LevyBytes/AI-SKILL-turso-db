# Rotate group credentials

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/auth/rotate' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "group not found"
}
```

POST /v1/organizations/{organizationSlug}/groups/{groupName}/auth/rotate

#### Authorizations

**`Authorization`** — string; header; required.

Include your auth token in a Bearer authentication header formatted `Bearer <token>`; `<token>` is the placeholder for that value.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug value for an organization or user account.

**`groupName`** — string; required.

The name of the group.

#### Response

Successful response (No Content)
