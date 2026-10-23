# Retrieve group configuration

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/configuration' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "delete_protection": true
}
```

GET /v1/organizations/{organizationSlug}/groups/{groupName}/configuration

#### Authorizations

**`Authorization`**: string; header; required.

Send a Bearer auth header formatted as `Bearer <token>`; use `<token>` as the authentication token value.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug identifying the organization or user account.

**`groupName`**: string; required.

The name of the group.

#### Response

200 - application/json

Successful response

**`delete_protection`**: boolean.

Block deletion of the group.

Example:

`true`
