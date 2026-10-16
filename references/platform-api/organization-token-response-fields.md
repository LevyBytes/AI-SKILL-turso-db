# Organization token response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `tokens`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `name` | string | Not declared | The token name. |
| `id` | string | Not declared | The token ID. Pass this to the revoke endpoint. |
| `organization` | string | Not declared | The organization slug. Always equal to the path parameter for tokens returned here. |
| `group` | string | Not declared | The group name. Present only for group-scoped tokens. |
| `scopes` | string[] | Not declared | The expanded scope list. Present only for group-scoped tokens. |
| `owner` | object | Not declared | The user who minted the token. Returned to admins and owners so they can see who provisioned each token. |
| `owner.username` | string | Not declared | Nested field in the object shown above. |
| `owner.email` | string | Not declared | Nested field in the object shown above. |
| `created_at` | string | Not declared | The date the token was created. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/api-tokens`: [official operation contract](https://docs.turso.tech/api-reference/tokens/list-organization).
