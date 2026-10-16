# PHP installation and replica connections

[Context index](kotlin-and-php-sdk-workflows.md) · [Continue](php-remote-queries-and-sync.md)

## Quickstart

This PHP quickstart covers how to:
- Install libSQL with Composer
- Retrieve database credentials
- Connect to a Turso database
- Execute a query using SQL
- Synchronize changes to a local database when needed (optional)
### Step 1: Retrieve database credentials

You need an existing database before moving on. If one does not exist yet, [create one](https://docs.turso.tech/quickstart). Then fetch the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Add the credentials to the environment variables in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Store these values as environment variables.

### Step 2: Install

Use composer to install the package into your project:

```
composer require turso/libsql

```

### Step 3: Connect

Next, connect to the local or remote database with the libSQL connector:

Embedded Replica

```
use Libsql\Database;

$db = new Database(
    path: 'test.db',
    url: getenv('TURSO_DATABASE_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
);
$conn = $db->connect();

```

Local only

```
use Libsql\Database;

$db = new Database(path: "database.db");

```

Or just:

```
use Libsql\Database;

$db = new Database("database.db");

```
