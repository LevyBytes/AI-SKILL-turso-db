# Rails adapter installation and configuration

[Context index](activerecord-and-authentication-workflows.md) · [Continue](rails-migrations-models-and-controllers.md)

## Prerequisites

This feature remains in technical preview. To share problems you find, use [the Turso Discord](https://discord.gg/turso).

### Prerequisites

 Before continuing, verify that you have the following in place:
- Follow the [Turso CLI installation guide](https://docs.turso.tech/cli/installation).
- Create or access your Turso account through the [Turso signup or login flow](https://docs.turso.tech/cli/authentication#signup).
- Start with an existing Rails app; see [Rails getting started](https://guides.rubyonrails.org/getting_started.html).
### Step 1: Install the libsql_activerecord Rubygem

Put this entry in your Gemfile:

```
gem 'libsql_activerecord'

```

Then run:

```
bundle install

```

### Step 2: Configure database credentials

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

Place your Turso credentials in your Rails credentials file or provide them through environment variables.

### Step 3: Use the libsql adapter

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
