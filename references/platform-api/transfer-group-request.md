# Transfer group request

[Context index](group-api-and-sdk-provisioning.md) · [Continue](transfer-group-response-fields.md)

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/transfer' \
-H 'Authorization: Bearer TOKEN' \
-H 'Content-Type: application/json' \
-d '{
    "organization": "new-organization-slug"
}'

```

```
{
  "name": "default",
  "version": "v0.23.7",
  "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
  "locations": [
    "aws-us-east-1"
  ],
  "primary": "us-east-1",
  "delete_protection": false
}
```

POST /v1/organizations/{organizationSlug}/groups/{groupName}/transfer

You may transfer groups only to organizations that you own or where you are an admin. Existing database URL and tokens keep working, but update your application to the new URL and token as soon as you can.

#### Authorizations

**`Authorization`** — string; header; required.

Pass a Bearer authentication header in the shape `Bearer <token>` and set `<token>` to your auth token.

#### Path Parameters

**`organizationSlug`** — string; required.

Slug for the organization or user account in the path.

**`groupName`** — string; required.

The name of the group.
