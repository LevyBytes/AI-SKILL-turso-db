# Organization member response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `members`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `username` | string | Not declared | The username for the member. |
| `role` | string | Not declared | The role assigned to the member. Values: `owner`, `admin`, `member`, `viewer`. |
| `email` | string | Not declared | The email for the member. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/members`: [official operation contract](https://docs.turso.tech/api-reference/organizations/members/list).
- Open when using `GET /v1/organizations/{organizationSlug}/members/{username}`: [official operation contract](https://docs.turso.tech/api-reference/organizations/members/retrieve).
