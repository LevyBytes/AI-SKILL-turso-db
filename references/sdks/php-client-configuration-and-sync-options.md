# PHP client configuration and sync options

[Context index](kotlin-and-php-sdk-workflows.md) · [Continue](php-statements-transactions-and-sync.md)

## Installing

 Use composer to add the package to your project:

```
composer require turso/libsql

```

### Initializing

 Add `use Libsql\Database` so the `Database` object is available.

```
$db = new Database("local.db");

```

### In-Memory Databases

 libSQL can connect to [in-memory databases](https://www.sqlite.org/inmemorydb.html) when persistence is unnecessary:

```
$db = new Database(":memory:");

```

 Or the simpler:

```
$db = new Database();

```

### Local Development

 For local development, pass a path as the first parameter.

```
$db = new Database("local.db");

```

 Or more explicitly:

```
$db = new Database(path: "local.db");

```

### Remote Only

 A remote only database can be used by passing `url` and `authToken`.

```
$db = new Database(
    url: getenv('TURSO_DATABASE_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
);

```

### Embedded Replicas

 Embedded replicas are configured by passing a `path`, `url`, and `authToken`. They can synchronize from the remote URL and delegate writes to the remote primary database:

```
$db = new Database(
    path: 'test.db',
    url: getenv('TURSO_DATABASE_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
);

```

### Synchronization Scheduling

The inspected PHP wrapper accepts a `syncInterval` constructor argument but does not forward it to the native database descriptor. Do not rely on that argument for periodic synchronization with this implementation. Call `$db->sync()` explicitly for an embedded replica, or schedule it in the application after checking the installed wrapper's behavior.

Contract checked against [the PHP Database implementation](https://github.com/tursodatabase/libsql-php/blob/4f6c5669ee844074865a1ea4935d5440ebd28571/src/Database.php).
