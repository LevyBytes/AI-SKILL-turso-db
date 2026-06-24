# Activerecord, Authentication, Authorization

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Prerequisites; Quickstart; Database URL; Scoping Levels; Permission Format; Managing JWKS Endpoints; Invalidating Tokens.

## Prerequisites

This feature remains in technical preview. To share problems you find, use [the Turso Discord](https://discord.gg/turso).

### Prerequisites

 Before continuing, verify that you have the following in place:
- Follow the [Turso CLI installation guide](https://docs.turso.tech/cli/installation).
- Create or access your Turso account through the [Turso signup or login flow](https://docs.turso.tech/cli/authentication#signup).
- Start with an existing Rails app; see [Rails getting started](https://guides.rubyonrails.org/getting_started.html).
 1

Install the libsql_activerecord Rubygem

Put this entry in your Gemfile:

```
gem 'libsql_activerecord'

```

Then run:

```
bundle install

```

2

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Store the credential values in `.env` as environment variables.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Place your Turso credentials in your Rails credentials file or provide them through environment variables.3

Use the libsql adapter

```
default: &default
  adapter: libsql
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

development:
  <<: *default
  url: <%= ENV['TURSO_DATABASE_URL'] %>
  auth_token: <%= ENV['TURSO_AUTH_TOKEN'] %>
  path: storage/development.sqlite3 # For embedded replica

```

4

Create and run migrations

Generate a migration:

```
rails generate migration CreateProducts name:string description:text

```

Run the migration:

```
rails db:migrate

```

5

Define models

Define a model at `app/models/product.rb`:

```
class Product < ApplicationRecord
  validates :name, presence: true
end

```

6

Use in controllers

Your controllers can then call ActiveRecord methods against the Turso database:

```
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end

  def create
    @product = Product.new(product_params)
    if @product.save
      redirect_to @product, notice: 'Product was successfully created.'
    else
      render :new
    end
  end

  private

  def product_params
    params.require(:product).permit(:name, :description)
  end
end

```

7

Execute raw SQL (if needed)

Raw SQL can be run as well:

```
results = ActiveRecord::Base.connection.execute("SELECT * FROM products")

```

### Examples

### Ruby on Rails App

See the full source code

## Quickstart

This feature remains in technical preview. To share problems you find, use [the Turso Discord](https://discord.gg/turso). This Ruby quickstart covers how to:
- Retrieve database credentials
- Install the libSQL ActiveRecord gem
- Open a connection to either a local or remote Turso database
- Define and create models
- Perform basic CRUD operations
- Execute raw SQL queries
- Work with migrations
 1

Retrieve database credentials

Continue with an existing database. If none exists yet, [create one](https://docs.turso.tech/quickstart).Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Store the credential values in `.env` as environment variables.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

2

Install

Update your Ruby project's Gemfile with these gems:

```
gem 'libsql_activerecord'
gem 'activerecord'

```

Then run:

```
bundle install

```

3

Connect

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

4

Define models

Add model files for the tables in your database. For instance, `product.rb`:

```
class Product < ActiveRecord::Base
  validates :name, presence: true
end

```

5

Create and execute a migration

Add a migration file, such as `001_create_products.rb`:

```
class CreateProducts < ActiveRecord::Migration[8.0]
  def change
    create_table :products do |t|
      t.string :name
      t.text :description
      t.timestamps
    end
  end
end

```

Execute the migration:

```
require_relative 'database'
require_relative '001_create_products'

CreateProducts.migrate(:up)

```

6

Execute

Perform some basic CRUD operations:

```
product = Product.create(name: 'Book', description: 'A book about books')

```

7

Work with associations

Define associations in your models:

```
class Author < ActiveRecord::Base
  has_many :books
end

class Book < ActiveRecord::Base
  belongs_to :author
end

```

Use associations in your code:

```
author = Author.create(name: 'Jane Doe')
book = author.books.create(title: 'My First Book')

puts author.books.count # => 1
puts book.author.name # => "Jane Doe"

```

## Database URL

SDKs use the `libsql://` protocol when connecting to Turso, except when the [HTTP API](https://docs.turso.tech/sdk/http) is used.

### Database URL

 The database URL is available from the [Turso CLI](https://docs.turso.tech/cli/db/show) or the [Platform API](https://docs.turso.tech/api-reference/databases/retrieve), and it has a form similar to this:

```
libsql://[DB-NAME]-[ORG-NAME].turso.io

```

 For repeated queries over an open connection, WebSockets often perform better; for one-off queries, HTTP may be faster because it can reduce client-server round trips. Benchmark both paths for your workload.

### Auth Tokens

 SDKs need an auth token except when [working locally](https://docs.turso.tech/local-development). Tokens can be created with the [Turso CLI](https://docs.turso.tech/cli) or [Platform API](https://docs.turso.tech/api-reference), or issued by an authentication provider through [JWKS](https://docs.turso.tech/sdk/authorization#jwks-authentication-providers). For token creation, fine-grained permissions, and JWKS configuration, see [Authorization](https://docs.turso.tech/sdk/authorization).

## Scoping Levels

Turso controls database access with scoped JWT-based tokens. A token may limit database access, permission level, and expiration, so each client receives only the access you define.

### Scoping Levels

 Token scope can be set at several levels, ordered from widest to narrowest:
| Level | Scope | How to create |
| --- | --- | --- |
| **Group** | Permit access to every database in a group | `turso group tokens create <group>` |
| **Database** | Access a single database | `turso db tokens create <database>` |
| **Read-only** | Queries only, no writes | Add `--read-only` flag |
| **Table + Action** | Specific tables and operations | Add `-p <table>:<actions>` flag |
| **Time-limited** | Auto-expires after a duration | Add `--expiration 7d` flag |

 Scopes can be layered. For example, this creates a read-only token for one database with a 7-day lifetime:

```
turso db tokens create mydb --read-only --expiration 7d

```

 Another option is a token that permits reads on every table and inserts only into `comments`:

```
turso db tokens create mydb \
  -p all:data_read \
  -p comments:data_add

```

### Issuing Tokens

 Tokens can be issued in either of these ways:
- **[Platform Tokens](https://docs.turso.tech/sdk/authorization/tokens)** - Generate tokens directly with the Turso CLI or Platform API.
- **[External Auth Providers](https://docs.turso.tech/sdk/authorization/jwks)** - Have an authentication provider, such as Clerk or Auth0, create tokens through JWKS.
 Both options can use [fine-grained permissions](https://docs.turso.tech/sdk/authorization/fine-grained-permissions) for table-level and action-level access control.

### Using Tokens

 When constructing a database client, provide any token through `authToken`:

```
import { createClient } from "@tursodatabase/serverless";

const db = createClient({
  url: "<your-database-url>",
  authToken: "<your-token>",
});

```

 Run `turso db show <database-name> --url` to retrieve the database URL.

## Permission Format

Fine-grained permissions define table-level and action-level access. They are supported for both [CLI-generated tokens](https://docs.turso.tech/sdk/authorization/tokens) and [JWKS-issued tokens](https://docs.turso.tech/sdk/authorization/jwks).

### Permission Format

 Permissions use the pattern `<table-name|all>:<action1>,<action2>`:

```
# Read-only access to all tables
turso db tokens create mydb -p all:data_read

# Specific actions on a specific table
turso db tokens create mydb -p users:data_read,data_update

# Multiple permission rules
turso db tokens create mydb \
  -p all:data_read \
  -p comments:data_add,data_update \
  -p posts:data_add,data_update,data_delete

```

 Set the table name to `all` when the permission should cover every table.

### Available Actions

| Action | Description |
| --- | --- |
| `data_read` | Read data from tables |
| `data_add` | Insert new rows |
| `data_update` | Update existing rows |
| `data_delete` | Delete rows |
| `schema_add` | Create new tables |
| `schema_update` | Modify table schemas |
| `schema_delete` | Drop tables |

 By default, `data_read` is permitted for SQLite system tables, such as `sqlite_master` and `sqlite_schema`, so users can inspect database metadata.

### Role-Based Access Example

 A JWT template can assign permissions according to user roles:
| Role | Permissions |
| --- | --- |
| Admin | `all:data_read,data_add,data_update,data_delete,schema_add,schema_update,schema_delete` |
| Moderator | `all:data_read,data_update` |
| User | `all:data_read` |

 With JWKS, the JWT token carries these permissions from your auth provider's user metadata, such as role or group membership.

## Managing JWKS Endpoints

Rather than handling tokens yourself, you can have your authentication provider create JWT tokens with JWKS and reuse your existing auth system, such as Clerk or Auth0. During the Turso Beta, Clerk & Auth0 are the only supported OIDC providers. 1

Generate JWT Template

Generate a JWT claims template for your auth provider with the Turso CLI:

```
# Full access to a database
turso org jwks template --database <database-name> --scope full-access

# Read-only access to a group
turso org jwks template --group <group-name> --scope read-only

# Fine-grained permissions
turso org jwks template \
  --database <database-name> \
  --permissions all:data_read \
  --permissions comments:data_add

```

Paste the generated template into the JWT configuration for your auth provider.2

Add JWKS Endpoint to Turso

Add your auth provider's JWKS endpoint to your Turso organization:

```
turso org jwks save <name> <url>

```

For example, with Clerk:

```
turso org jwks save clerk https://your-app.clerk.accounts.dev/.well-known/jwks.json

```

JWKS endpoints can also be added from organization settings in the [Turso Dashboard](https://turso.tech/app).3

Use Tokens in Your Application

Retrieve the JWT from your auth provider, then supply it through `authToken`:

```
import { createClient } from "@tursodatabase/serverless";

const authToken = await getAuthToken(); // e.g., from Clerk, Auth0

const db = createClient({
  url: "<your-database-url>",
  authToken,
});

```

### Managing JWKS Endpoints

```
# List all JWKS endpoints
turso org jwks list

# Remove a JWKS endpoint
turso org jwks remove <name>

```

 When no JWT template defines specific permissions, generated tokens default to access for **all databases in all groups**.

## Invalidating Tokens

Use the CLI to create tokens for either one database or every database in a group:

```
# Token for a single database
turso db tokens create <database-name>

# Token for all databases in a group
turso group tokens create <group-name>

```

 Token scope, permissions, and expiration can be adjusted:
| Flag | Description |
| --- | --- |
| `-r`, `--read-only` | Generate a read-only token that allows queries but blocks writes |
| `-p`, `--permissions` | Define fine-grained permissions for each table and action |
| `-e`, `--expiration` | Set token expiration (`never`, `7d`, `30d`, etc.) |

```
turso db tokens create mydb --read-only

```

 Tokens may also be generated through the [Platform API](https://docs.turso.tech/api-reference/databases/create-token).

### Invalidating Tokens

 To rotate signing keys, invalidate all current tokens for a database or group:

```
# Invalidate all tokens for a database
turso db tokens invalidate <database-name>

# Invalidate all tokens for a group (and all its databases)
turso group tokens invalidate <group-name>

```
