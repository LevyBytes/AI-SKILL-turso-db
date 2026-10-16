# SQLite upload and database tokens

[Context index](access-durability-encryption-and-migration.md) · [Setup and preceding context](sqlite-migration-wal-preparation.md) · [Previous phase](sqlite-import-and-platform-authentication.md)

### Step 4: Create a Database for Import

Start by creating a database prepared for an import:

```
curl -X POST "https://api.turso.tech/v1/organizations/{organizationSlug}/databases" \
  -L \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
      "name": "new-database",
      "group": "default",
      "seed": { "type": "database_upload" }
  }'

```

The `seed` parameter containing `"type": "database_upload"` signals that a database file upload will follow. If the upload is not completed, this database still counts against your quota and remains unusable.

### Step 5: Create a Database Token

Create an authentication token for the database:

```
curl -X POST "https://api.turso.tech/v1/organizations/{organizationSlug}/databases/{databaseName}/auth/tokens" \
  -L \
  -H "Authorization: Bearer TOKEN"

```

Use this token to authenticate the upload request and later database requests.

### Step 6: Upload Your SQLite Database

Last, upload the SQLite database file:

```
curl -X POST "https://{databaseName}-{organizationSlug}.turso.io/v1/upload" \
  -H "Authorization: Bearer DATABASE_TOKEN" \
  --data-binary @/path/to/your/database.db

```

The `Authorization` header must use the database token created in the previous step, not the Platform API token. You can then connect to the new Turso database with any Turso client library.

### TypeScript / JS

### Rust

### Go

### Python

### Android

### Swift

### PHP

### Ruby

### Ruby on Rails

### C

### libSQL Remote Protocol
