# Unarchive group

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/unarchive' \
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

POST /v1/organizations/{organizationSlug}/groups/{groupName}/unarchive

On the free plan, databases are archived after 10 days without activity. The API can unarchive inactive groups.

#### Authorizations

**`Authorization`**: string; header; required.

For authorization, send a Bearer authentication header with `Bearer <token>` as its value; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Organization or user account slug for this request.

**`groupName`**: string; required.

The name of the group.

#### Response

Successful response

**`group`**: object.

The group that was unarchived

Open when you need the nested `group` contract: [field details](group-response-fields.md).
