# Group response fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `group`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `name` | string | Not declared | The group name, unique across your organization. |
| `version` | string | Not declared | The current libSQL server version the databases in that group are running. Deprecated in this API contract. |
| `uuid` | string | Not declared | The group universal unique identifier (UUID). |
| `locations` | string[] | Not declared | An array of location keys the group is located. Deprecated in this API contract. |
| `primary` | string | Not declared | The primary location key. |
| `delete_protection` | boolean | Not declared | The current status for delete protection. If enabled, the group and all its databases cannot be deleted. |

## Operations

- Open when using `POST /v1/organizations/{organizationSlug}/groups`: [official operation contract](https://docs.turso.tech/api-reference/groups/create).
- Open when using `DELETE /v1/organizations/{organizationSlug}/groups/{groupName}`: [official operation contract](https://docs.turso.tech/api-reference/groups/delete).
- Open when using `GET /v1/organizations/{organizationSlug}/groups`: [official operation contract](https://docs.turso.tech/api-reference/groups/list).
- Open when using `GET /v1/organizations/{organizationSlug}/groups/{groupName}`: [official operation contract](https://docs.turso.tech/api-reference/groups/retrieve).
- Open when using `POST /v1/organizations/{organizationSlug}/groups/{groupName}/unarchive`: [official operation contract](https://docs.turso.tech/api-reference/groups/unarchive).
