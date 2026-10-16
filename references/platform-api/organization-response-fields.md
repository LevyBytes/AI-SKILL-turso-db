# Organization response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `organization`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `name` | string | Not declared | The organization name. Every user has a `personal` organization for their own account. |
| `slug` | string | Not declared | The organization slug. This will be your username for `personal` accounts. |
| `type` | string | Not declared | The type of account this organization is. Will always be `personal` or `team`. Values: `personal`, `team`. |
| `overages` | boolean | Not declared | Whether paid overages are enabled for the organization. |
| `require_mfa` | boolean | Not declared | Whether all members of the organization are required to have multi-factor authentication enabled. |
| `blocked_reads` | boolean | Not declared | Returns the current status for blocked reads. |
| `blocked_writes` | boolean | Not declared | Returns the current status for blocked writes. |
| `plan_id` | string | Not declared | The pricing plan identifier this organization is subscribed to. |
| `plan_timeline` | string | Not declared | The billing cycle for the paid plan, if any. |
| `platform` | string | Not declared | The external platform this organization is managed by. Will be empty for Turso managed organizations. |
| `restore_enabled` | boolean | Not declared | Whether deleted database and group recovery is enabled for the organization. Returned when retrieving a single organization. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}`: [official operation contract](https://docs.turso.tech/api-reference/organizations/retrieve).
- Open when using `PATCH /v1/organizations/{organizationSlug}`: [official operation contract](https://docs.turso.tech/api-reference/organizations/update).
