# Retrieve group details

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}' \
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

GET /v1/organizations/{organizationSlug}/groups/{groupName}

#### Authorizations

**`Authorization`**: string; header; required.

Authorize the call with a Bearer authentication header whose value is `Bearer <token>`; `<token>` holds the auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Slug belonging to the target organization or user account.

**`groupName`**: string; required.

The name of the group.

#### Response

Successful response

**`group`**: object.

Open when you need the nested `group` contract: [field details](group-response-fields.md).
