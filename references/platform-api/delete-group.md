# Delete group

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

DELETE /v1/organizations/{organizationSlug}/groups/{groupName}

#### Authorizations

**`Authorization`**: string; header; required.

The request uses a Bearer authentication header: `Bearer <token>`, where `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

The organization slug or user account slug.

**`groupName`**: string; required.

The name of the group.

#### Response

Successful response

**`group`**: object.

Open when you need the nested `group` contract: [field details](group-response-fields.md).
