# Audit log response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `audit_logs`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `code` | string | Not declared | The code associated to the action taken. Values: `user-signup`, `db-create`, `db-delete`, `db-protect`, `db-unprotect`, `db-token-create`, `group-token-create`, `user-token-create`, `instance-create`, `instance-delete`, `org-create`, `org-delete`, `org-member-add`, `org-member-rm`, `org-member-leave`, `org-plan-update`, `org-set-overages`, `group-create`, `group-delete`, `group-unarchive`, `group-protect`, `group-unprotect`, `db-aunrchive`, `user-delete`. |
| `message` | string | Not declared | Additional context from the performed action. |
| `origin` | string | Not declared | Where this action was performed. Will be either `cli` or `web` depending on the `User-Agent` sent to the API. |
| `author` | string | Not declared | The username of the user who performed the action. |
| `created_at` | string | Not declared | A formatted [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) timestamp this action was performed. |
| `data` | object | Not declared | The payload of the action performed. |

## Response: `pagination`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `page` | integer | Not declared | The current page number. |
| `page_size` | integer | Not declared | The number of items per page. |
| `total_pages` | integer | Not declared | The total number of pages. |
| `total_rows` | integer | Not declared | The total number of items. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/audit-logs`: [official operation contract](https://docs.turso.tech/api-reference/audit-logs/list).
