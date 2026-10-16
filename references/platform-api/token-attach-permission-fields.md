# Token attach permission fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Body: `permissions`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `read_attach` | object | Not declared | Read `ATTACH` permission for the token. |
| `read_attach.databases` | string[] | Not declared | Nested field in the object shown above. |

## Operations

- Open when using `POST /v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens`: [official operation contract](https://docs.turso.tech/api-reference/databases/create-token).
- Open when using `POST /v1/organizations/{organizationSlug}/groups/{groupName}/auth/tokens`: [official operation contract](https://docs.turso.tech/api-reference/groups/create-token).
