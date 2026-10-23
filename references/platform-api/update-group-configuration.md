# Update group configuration

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "delete_protection": true
  }'

```

```
{
  "delete_protection": true
}
```

PATCH /v1/organizations/{organizationSlug}/groups/{groupName}/configuration

#### Authorizations

**`Authorization`**: string; header; required.

Set the Bearer authentication header to `Bearer <token>` using your auth token as `<token>`.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug for the selected organization or user account.

**`groupName`**: string; required.

The name of the group.

#### Body

application/json

Configuration that will be applied to the selected group.

**`delete_protection`**: boolean.

Keep the group from being deleted.

Example:

`true`

#### Response

200 - application/json

Successful response

**`delete_protection`**: boolean.

Indicates whether group deletion is blocked.

Example:

`true`
