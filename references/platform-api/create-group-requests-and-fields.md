# Create group requests and fields

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups' \
-H 'Authorization: Bearer TOKEN' \
-H 'Content-Type: application/json' \
-d '{
    "name": "new-group",
    "location": "lhr"
}'

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

POST /v1/organizations/{organizationSlug}/groups

Additional groups can be created only on Scaler, Pro, and Enterprise plans.

#### Authorizations

**`Authorization`**: string; header; required.

Provide the Bearer authentication header in the `Bearer <token>` format; set `<token>` to your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Organization or user account slug.

#### Body

application/json

Data used to create a new group.

**`name`**: string; required.

Name assigned to the new group.

**`location`**: string; required.

Location key assigned to the new group.

**`extensions`**: string or string[].

Extensions enabled for new databases created in this group. For vector extensions, users should use the native [libSQL vector datatype](https://docs.turso.tech/features/ai-and-embeddings) instead.

Available options: `all`

#### Response

Successful response

**`group`**: object.

The newly created group

Open when you need the nested `group` contract: [field details](group-response-fields.md).
