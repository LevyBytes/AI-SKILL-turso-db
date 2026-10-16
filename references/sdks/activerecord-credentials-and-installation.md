# Activerecord credentials and installation

[Context index](activerecord-and-authentication-workflows.md) · [Continue](activerecord-connections-and-models.md)

## Quickstart

This feature remains in technical preview. To share problems you find, use [the Turso Discord](https://discord.gg/turso). This Ruby quickstart covers how to:
- Retrieve database credentials
- Install the libSQL ActiveRecord gem
- Open a connection to either a local or remote Turso database
- Define and create models
- Perform basic CRUD operations
- Execute raw SQL queries
- Work with migrations
### Step 1: Retrieve database credentials

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

### Step 2: Install

Update your Ruby project's Gemfile with these gems:

```
gem 'libsql_activerecord'
gem 'activerecord'

```

Then run:

```
bundle install

```
