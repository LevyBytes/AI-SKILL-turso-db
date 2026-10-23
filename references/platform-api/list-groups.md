# List groups

[Context index](group-api-and-sdk-provisioning.md)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/groups \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "groups": [
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
  ]
}
```

GET /v1/organizations/{organizationSlug}/groups

#### Authorizations

**`Authorization`**: string; header; required.

Use the Bearer header value `Bearer <token>` to authenticate; `<token>` is your auth token.

#### Path Parameters

**`organizationSlug`**: string; required.

Identifier slug for the organization or user account.

#### Response

200 - application/json

Successful response

**`groups`**: object[].

Open when you need the nested `groups` contract: [field details](group-response-fields.md).
