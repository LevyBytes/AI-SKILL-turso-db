# Database query usage fields

Open when constructing or interpreting the nested fields used by the linked Platform API operation. Array-item rows describe one element; a required flag applies within its immediate object. A blank required set in the source is not a guarantee that a response field will always be omitted or present.

## Response: `top_queries`

| Field | Type | Required in source | Meaning and constraints |
| --- | --- | --- | --- |
| `query` | string | Not declared | A string representing the SQL query executed. |
| `rows_read` | integer | Not declared | An integer indicating the number of rows read by the query, which reflects the volume of data the query processed from the database. |
| `rows_written` | integer | Not declared | An integer indicating the number of rows written (inserted, updated, or deleted) by the query, which reflects the impact of the query on the database data. |

## Operations

- Open when using `GET /v1/organizations/{organizationSlug}/databases/{databaseName}/stats`: [official operation contract](https://docs.turso.tech/api-reference/databases/stats).
