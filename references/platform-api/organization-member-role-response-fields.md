# Organization member role response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `member`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `username` | string | Not declared | The username of the updated member. |
| `email` | string | Not declared | The email of the updated member. |
| `role` | string | Not declared | The new role of the updated member. Values: `admin`, `member`, `viewer`. |

## Operations

- Open when using `PATCH /v1/organizations/{organizationSlug}/members/{username}`: [official operation contract](https://docs.turso.tech/api-reference/organizations/members/update).
