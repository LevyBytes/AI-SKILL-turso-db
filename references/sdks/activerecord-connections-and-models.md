# Activerecord connections and models

[Context index](activerecord-and-authentication-workflows.md) · [Setup and preceding context](activerecord-credentials-and-installation.md) · [Continue](activerecord-migrations-crud-and-associations.md)

### Step 3: Connect

Add a Ruby file, such as `database.rb`, for configuring the database connection:

Embedded Replicas

```
require 'libsql_activerecord'
require 'active_record'

ActiveRecord::Base.establish_connection(
  adapter: 'libsql',
  url: ENV['TURSO_DATABASE_URL'],
  auth_token: ENV['TURSO_AUTH_TOKEN'],
  path: 'path/to/local/replica.db'
)

```

Local only

```
require 'libsql_activerecord'
require 'active_record'

ActiveRecord::Base.establish_connection(
  adapter: 'libsql',
  path: 'path/to/local.db'
)

```

Remote only

```
require 'libsql_activerecord'
require 'active_record'

ActiveRecord::Base.establish_connection(
  adapter: 'libsql',
  url: ENV['TURSO_DATABASE_URL'],
  auth_token: ENV['TURSO_AUTH_TOKEN']
)

```

In-memory

```
require 'libsql_activerecord'
require 'active_record'

ActiveRecord::Base.establish_connection(adapter: 'libsql')

```

### Step 4: Define models

Add model files for the tables in your database. For instance, `product.rb`:

```
class Product < ActiveRecord::Base
  validates :name, presence: true
end

```
