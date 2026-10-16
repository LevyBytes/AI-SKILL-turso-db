# Laravel remote replicas and migration schema

[Context index](kotlin-and-php-sdk-workflows.md) · [Setup and preceding context](laravel-installation-and-local-configuration.md) · [Continue](laravel-migrations-and-eloquent-inserts.md)

Remote only

Update your `config/database.php`:

```
return [
    "default" => env("DB_CONNECTION", "libsql"),

    "connections" => [
        "libsql" => [
            "driver" => "libsql",
            "url" => env("TURSO_DATABASE_URL"),
            "password" => env("TURSO_AUTH_TOKEN"),
        ],
        // ...
    ],
];

```

Next, add the following values to `.env`:

```
DB_CONNECTION=libsql
TURSO_DATABASE_URL=libsql://...
TURSO_AUTH_TOKEN=...

```

Embedded Replicas

Update your `config/database.php`:

```
return [
    "default" => env("DB_CONNECTION", "libsql"),

    "connections" => [
        "libsql" => [
            "driver" => "libsql",
            "database" => database_path("database.db"),
            "url" => env("TURSO_DATABASE_URL"),
            "password" => env("TURSO_AUTH_TOKEN"),
            "sync_interval" => env("TURSO_SYNC_INTERVAL", 300),
        ],
        // ...
    ],
];

```

Next, add the following values to `.env`:

```
DB_CONNECTION=libsql
TURSO_DATABASE_URL=libsql://...
TURSO_AUTH_TOKEN=...
TURSO_SYNC_INTERVAL=300

```

`sync_interval` is optional and defaults to 300 seconds (5 minutes).

### Step 4: Create a model and migration

Generate a User model and its migration:

```
php artisan make:model User -m

```

Update the migration file:

```
public function up()
{
    Schema::create('users', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email')->unique();
        $table->timestamps();
    });
}

```
