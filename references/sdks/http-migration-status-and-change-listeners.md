# HTTP migration status and change listeners

[Context index](http-api-and-package-selection.md) · [Setup and preceding context](http-authentication-and-pipeline-lifecycle.md) · [Previous phase](http-transactions-results-and-service-endpoints.md)

### Schema Migration Status

 Turso offers a managed multi-tenant schema system called [Multi-DB Schemas](https://docs.turso.tech/features/multi-db-schemas). It lets one parent database automatically share its schema with related child databases, and parent schema changes propagate to those databases. Use `/v1/jobs` to track schema migration status. This is currently unavailable on AWS for Free, Developer and Scaler plans.

### `GET /v1/jobs`

 Returns a summary for every schema migration job:

```
curl -X GET 'https://[databaseName]-[organizationSlug].turso.io/v1/jobs' \
     -H 'Authorization: Bearer TOKEN'

```

#### Response Fields

| Field | Type | Meaning |
| --- | --- | --- |
| `schema_version` | number | Applied schema version |
| `migrations` | array | Migration jobs |

##### Migration Object

`job_id` (number) identifies the migration. `status` (string) has these states: `WaitingDryRun`, `DryRunSuccess`, `DryRunFailure`, `WaitingRun`, `RunSuccess`, `RunFailure`

### `GET /v1/jobs/:id`

 Returns details for one migration job.

```
curl -X GET 'https://[databaseName]-[organizationSlug].turso.io/v1/jobs/:id' \
     -H 'Authorization: Bearer TOKEN'

```

#### Path Parameters

`id` (number, required) identifies the migration job.

#### Response Fields

| Field | Type | Meaning |
| --- | --- | --- |
| `job_id` | number | Migration identifier |
| `status` | string | Job state, as in `/v1/jobs` |
| `error` | string or null | Failure message |
| `progress` | array | Per-database migration states |

##### Progress Object

`namespace` (string) names the database. `status` (string) has these states: `Enqueued`, `DryRunSuccess`, `DryRunFailure`, `Run`, `Success`, `Failure` `error` is a string when that database migration fails, otherwise null.

### Listen to changes

 Listen for changes committed to the database through `/beta/listen`: This feature is in technical preview. Report problems via [Join us in Discord](https://tur.so/discord). It is currently unavailable on AWS for Free, Developer and Scaler plans. The database group must run `v0.24.18` or later - `turso group update <group-name> --version latest`

### `GET /beta/listen`

 Request

```
curl -L 'https://[primary-instance-id]-[databaseName]-[organizationSlug].turso.io/beta/listen?table=TABLE_NAME&action=insert' \
-H 'Authorization: Bearer TOKEN'

```

#### Query Parameters

| Parameter | Type | Required | Meaning |
| --- | --- | --- | --- |
| `table` | string | Yes | Table to observe |
| `action` | string | Yes | `insert`, `update` or `delete` |
