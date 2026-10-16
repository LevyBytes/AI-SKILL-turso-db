# Laravel installation and local configuration

[Context index](kotlin-and-php-sdk-workflows.md) · [Continue](laravel-remote-replicas-and-migration-schema.md)

## Laravel

This Laravel quickstart explains how to:
- Install the Turso Laravel adapter
- Configure Laravel to use Turso
- Create a model and migration
- Perform CRUD operations using Eloquent
### Step 1: Retrieve database credentials

Before continuing, you need an existing database. If you do not have one yet, [create one](https://docs.turso.tech/quickstart). Then get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Store the credentials in the environment variables inside `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

These should be kept as environment variables.

### Step 2: Install

Use Composer to install the package in your Laravel project:

```
composer require turso/libsql-laravel

```

Enable FFI in your PHP configuration before using the Laravel adapter.

### Step 3: Configure

Select the setup you want and apply the matching configuration:

Local only

Update your `config/database.php`:

```
return [
    "default" => env("DB_CONNECTION", "libsql"),

    "connections" => [
        "libsql" => [
            "driver" => "libsql",
            "database" => database_path("database.db"),
        ],
        // ...
    ],
];

```

This configuration uses a local SQLite file through the libSQL adapter.
