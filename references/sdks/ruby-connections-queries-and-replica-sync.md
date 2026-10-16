# Ruby connections queries and replica sync

[Context index](python-and-ruby-sdk-workflows.md) · [Setup and preceding context](ruby-installation-credentials-and-example-routes.md)

### Step 3: Connect

Embedded Replicas

```
require 'turso_libsql'

db =Libsql::Database.new(
  path: 'local.db',
  url: ENV['TURSO_DATABASE_URL'],
  auth_token: ENV['TURSO_AUTH_TOKEN'],
  sync_interval: 100
)

```

Local only

```
require 'turso_libsql'

db = Libsql::Database.new(path: 'local.db')

```

Remote only

```
require 'turso_libsql'

db = Libsql::Database.new(
  url: ENV['TURSO_DATABASE_URL'],
  auth_token: ENV['TURSO_AUTH_TOKEN']
)

```

### Step 4: Execute

To run SQL against the existing database, prepare a statement and execute it:

```
db.connect do |conn|
  rows = conn.query 'SELECT * FROM users'
  rows.close
end

```

Placeholders can be used for values when needed:

```
db.connect do |conn|
  name = 'Iku'
  conn.execute 'INSERT INTO users (name) VALUES (?)', [name]
end

```

### Step 5: Sync (Embedded Replicas only)

When using embedded replicas, call `sync` on the database to synchronize the local database with the primary database unless `sync_interval` is already configured. Calling `sync` while `sync_interval` is enabled is also acceptable:

```
db.sync

```
