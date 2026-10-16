# Shared schema API and migration restrictions

[Context index](replica-hosting-and-recovery-workflows.md) · [Setup and preceding context](shared-schema-creation-with-cli.md)

### Platform API

 Confirm that you have an API Token and know the Organization name.

### Step 1: Create Parent Database

Create a database with `is_schema` set to `true`:

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "parent-db",
      "group": "default",
      "is_schema": true
  }'

```

### Step 2: Create Schema

Use the shell, [SDK](https://docs.turso.tech/sdk), or a migration script to connect to the database and run `CREATE TABLE`, `CREATE TRIGGER`, `CREATE VIEW`, plus anything else needed to setup the schema: Turso CLI

```
turso db shell parent-db

```

### Step 3: Create Child Database(s)

Create one or more child databases, passing the parent database name in the `schema` field:

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "child-db",
      "group": "default",
      "schema": "parent-db"
  }'

```

### Step 4: Apply Additional Schema Changes

Additional schema changes can now be applied to the parent database, after which the child databases update automatically:

```
turso db shell parent-db "CREATE TABLE users (id INT PRIMARY KEY, name TEXT);"

```

### Things to know

- Schema databases cannot be shared across groups or globally through an organization/account.
- `INSERT` rows may be added to the parent database and queried by the child database(s), but this is not recommended.
  - Watch for constraints that could conflict with the child database(s).

- A parent database with one or more child databases cannot be deleted.
- Applying a migration to the schema database follows this sequence:
  - First, it runs as a dry-run on the schema database and all associated databases.
  - If the dry-run succeeds, a migration job is created.
  - Each database that references the schema gets its own task.
  - The migration is then executed on every referencing database.
  - When every task succeeds, the schema database itself receives the migration last.
  - While any migrations are running, you cannot create or delete a database.

- **During a migration, write operations lock on all databases.**
- Ensure applications querying child databases can handle databases that have not yet received the schema update.
- Schema changes cannot be applied directly to a child database; use the parent (schema) database instead.
- Migration status can be checked through the `/jobs` endpoint; [learn more](https://docs.turso.tech/sdk/http/reference#schema-migration-jobs).
