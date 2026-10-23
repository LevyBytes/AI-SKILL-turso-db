# Transfer group response fields

[Context index](group-api-and-sdk-provisioning.md) · [Setup and preceding context](transfer-group-request.md)

#### Body

application/json

Receiving Organization details.

**`organization`**: string.

Slug for the organization that will receive the group.

#### Response

Successful response

**`name`**: string.

Group name, unique within your organization.

Example:

`"default"`

**`version`**: string; deprecated.

The libSQL server version currently used by databases in this group.

Example:

`"v0.23.7"`

**`uuid`**: string.

The UUID that uniquely identifies the group.

Example:

`"0a28102d-6906-11ee-8553-eaa7715aeaf2"`

**`locations`**: string[]; deprecated.

Array containing the location keys where the group resides.

Example:

```
["aws-us-east-1"]
```

**`primary`**: string.

The primary location key.

Example:

`"us-east-1"`

**`delete_protection`**: boolean.

Current delete protection status. When enabled, neither the group nor any of its databases can be deleted.

Example:

`false`
