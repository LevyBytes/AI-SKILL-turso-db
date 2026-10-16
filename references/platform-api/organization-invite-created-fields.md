# Organization invite created fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `invited`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `email` | string | Not declared | The email of the person invited. |
| `role` | string | Not declared | The assigned role for the invited user. Values: `admin`, `member`, `viewer`. |
| `organization` | string | Not declared | The name of the organization the user was invited to. |

## Operations

- Open when using `POST /v2/organizations/{organizationSlug}/invites`: [official operation contract](https://docs.turso.tech/api-reference/organizations/invites/create-v2).
