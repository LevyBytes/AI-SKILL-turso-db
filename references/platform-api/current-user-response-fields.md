# Current user response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `user`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `username` | string | Not declared | The username of the user. |
| `name` | string | Not declared | The display name of the user. |
| `email` | string | Not declared | The primary email address of the user. |
| `avatarUrl` | string | Not declared | URL to the user's avatar image. |
| `plan` | string | Not declared | The user's current plan. |
| `mfa` | boolean | Not declared | Whether the user has multi-factor authentication enabled. |
| `has_pending_invites` | boolean | Not declared | Whether the user has pending organization invites waiting to be accepted or declined. |

## Operations

- Open when using `GET /v1/user`: [official operation contract](https://docs.turso.tech/api-reference/user/get-current).
