# Organization invite list fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `invites`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `id` | integer | Not declared | The unique ID for the invite. |
| `email` | string | Not declared | The email of the person invited. |
| `role` | string | Not declared | The assigned role for the invited user. Values: `admin`, `member`, `viewer`. |
| `created_at` | string | Not declared | The datetime the invite was created in [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format. |

## Operations

- Open when using `GET /v2/organizations/{organizationSlug}/invites`: [official operation contract](https://docs.turso.tech/api-reference/organizations/invites/list-v2).
