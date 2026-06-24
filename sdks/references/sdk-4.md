# Kotlin, Php

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Quickstart; Installing; examples; Laravel; Prerequisites.

## Quickstart

The feature is still in technical preview. Use [Join us in Discord](https://discord.gg/turso) to share problems you encounter. For now, it works only with the Android Gradle Plugin, and full Kotlin support is planned. This Kotlin quickstart covers how to:
- Retrieve database credentials
- Include libSQL as a dependency in your Android Gradle project
- Open a connection to either a local or remote Turso database
- Execute a query using SQL
- Synchronize changes into a local database when needed (optional)
 1

Retrieve database credentials

An existing database is required before continuing. If you do not already have one, [create one](https://docs.turso.tech/quickstart). Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Place the credentials in the environment variables defined in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Make sure database tokens are handled securely.2

Install

Configure libsql as an implementation dependency in Gradle. This currently works only with the Android Gradle Plugin.

```
dependencies {
    implementation("tech.turso.libsql:libsql:0.1.0")
}

```

3

Connect

Start by creating a `Database` object, then open a `Connection` from it:

Embedded Replicas

```
import tech.turso.libsql.Libsql

val db = Libsql.open(
    path = "./local.db",
    url = "TURSO_DATABASE_URL",
    authToken = "TURSO_AUTH_TOKEN",
)

val conn = db.connect()

```

Local only

```
import tech.turso.libsql.Libsql

val db = Libsql.open(path = "./local.db")
val conn = db.connect()

```

Remote only

```
import tech.turso.libsql.Libsql

val db = Libsql.open(
    url = "TURSO_DATABASE_URL",
    authToken = "TURSO_AUTH_TOKEN",
)

val conn = db.connect()

```

4

Execute

To run a SQL query against the existing database, call `execute()`:

```
db.connect().use {
    it.execute("INSERT INTO users (id) VALUES (1)")
}

```

Placeholders can be used when values need to be supplied separately:

```
db.connect().use {
    it.query("SELECT * FROM users WHERE id = ?", 1)
}

```

5

Sync (Embedded Replicas only)

For embedded replicas, call `sync()` on the database type to synchronize the local database with the primary database unless `syncInterval` is already in use. Calling `sync` while `syncInterval` is enabled is still allowed:

```
db.sync()

```

## Installing

The feature remains in technical preview. Use [Join us in Discord](https://discord.gg/turso) to report problems. At this stage, it works only with the Android Gradle Plugin, with full Kotlin support still coming.

### Installing

 Configure libsql as an implementation dependency in Gradle:

```
dependencies {
    implementation("tech.turso.libsql:libsql:0.1.0")
}

```

### In-Memory Databases

 libSQL can connect to [in-memory databases](https://www.sqlite.org/inmemorydb.html) when persistence is not needed:

```
import tech.turso.libsql.Libsql

val db = Libsql.open(":memory:")
val conn = db.connect()

```

### Local Development

 Local development can use an SQLite file:

```
import tech.turso.libsql.Libsql

val db = Libsql.open(path = "./local.db")
val conn = db.connect()

```

### Embedded Replicas

 Embedded replicas can synchronize from the remote URL while delegating writes to the remote primary database:

```
import tech.turso.libsql.Libsql

val db = Libsql.open(
    path = "./local.db",
    url = "TURSO_DATABASE_URL",
    authToken = "TURSO_AUTH_TOKEN",
)

val conn = db.connect()

```

### Manual Sync

 The `sync` function lets you manually synchronize the local database with its remote counterpart:

```
db.sync() // Call sync manually to update local database (only for EmbeddedReplicaDatabase)

```

### Simple query

 Pass a string into `query()` to run a SQL statement, with optional arguments when required:

```
db.connect().use {
    it.execute("INSERT INTO users VALUES (?)", 1)
}

```

### Placeholders

 libSQL allows positional and named placeholders inside SQL statements: Positional

```
db.connect().use {
    it.query("SELECT * FROM users WHERE id = ?", 1)
}

```

 Named

```
db.connect().use {
    it.query("SELECT * FROM users WHERE id = :id", mapOf(":id" to 1))
}

```

### Batch Transactions

 A batch runs several SQL statements in order inside an implicit transaction. The backend manages the transaction, committing every change on success and rolling everything back with no modifications if any statement fails.

```
db.connect().use {
    it.execute_batch("
      CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL
      );

      INSERT INTO users (name) VALUES ('Alice');
      INSERT INTO users (name) VALUES ('Bob');
    ")
}

```

### Transactions

 Interactive transactions in SQLite keep a sequence of read and write operations consistent within the transaction's scope. They let you decide when to commit or roll back changes while keeping that work isolated from other client activity.

```
db.connect().use {
    val tx = it.transaction();

    tx.execute("INSERT INTO users (name) VALUES (?)", "Iku");
    tx.execute("INSERT INTO users (name) VALUES (?)", "Iku 2");

    tx.commit() // or tx.rollback()
}

```

## examples

[tursodatabase](https://docs.turso.tech/tursodatabase)  / ** [turso-client-php](https://docs.turso.tech/tursodatabase/turso-client-php) ** Public
-

###  Uh oh!

The page failed to load. Reload it to try again.

- [Notifications](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fturso-client-php) Sign in before changing notification settings
- Open [Fork 13](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fturso-client-php)
- Open [Star  110](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Fturso-client-php)

### Expand file tree

/

### examples

/Copy path

### Directory actions

### More options

More options

### Directory actions

### More options

More options

### Latest commit

### History

[History](https://docs.turso.tech/tursodatabase/turso-client-php/commits/main/examples)History/

### examples

/Copy path

### Folders and files

| Name | Name | Last commit message | Last commit date |
| --- | --- | --- | --- |
| ### parent directory.. |  |  |  |
| sqld-offline | sqld-offline |  |  |
| local.php | local.php |  |  |
| memory.php | memory.php |  |  |
| offline-writes.php | offline-writes.php |  |  |
| remote-replica.php | remote-replica.php |  |  |
| remote.php | remote.php |  |  |

## Laravel

This Laravel quickstart explains how to:
- Install the Turso Laravel adapter
- Configure Laravel to use Turso
- Create a model and migration
- Perform CRUD operations using Eloquent
 1

Retrieve database credentials

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

These should be kept as environment variables.2

Install

Use Composer to install the package in your Laravel project:

```
composer require turso/libsql-laravel

```

Enable FFI in your PHP configuration before using the Laravel adapter.3

Configure

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

`sync_interval` is optional and defaults to 300 seconds (5 minutes).4

Create a model and migration

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

5

Run migrations

Run the migration to create the users table:

```
php artisan migrate

```

6

Execute CRUD operations

After that, use Eloquent to perform CRUD operations:

```
use App\Models\User;

$user = User::create([
    'name' => 'John Doe',
    'email' => 'john@example.com',
]);

```

## Prerequisites

(Figure: Doctrine DBAL)

### Prerequisites

 Before beginning, confirm that you:
- [Install the Turso CLI](https://docs.turso.tech/cli/installation) before continuing
- [Sign up or login to Turso](https://docs.turso.tech/cli/authentication#signup) first
- Use a PHP Application that includes Doctrine DBAL
 Get the newest extension/driver binary build from the [Release](https://github.com/tursodatabase/turso-client-php/releases) page. It is available for:
- Linux
- Mac/Darwin
- Windows (WSL) / [Devcontainer](https://github.com/darkterminal/turso-docker-php)

Turso PHP Installer is limited to Linux/macOS only

Get the installer:

```
curl --proto '=https' --tlsv1.2 -sSf "https://darkterminal.github.io/turso-php-installer/dist/turso-php-installer.phar"
./turso-php-installer.phar

```

Register it as a global executable script:

```
sudo mv turso-php-installer.phar /usr/local/bin/turso-php-installer

```

**Usage**Install Turso Client PHP / libSQL Extension:

```
turso-php-installer install

```

Check on your console/terminal

```
$ php --m | grep libsql
liblibsql_php

```

Update Turso Client PHP / libSQL Extension:

```
turso-php-installer update

```

Uninstall Turso Client PHP / libSQL Extension:

```
turso-php-installer uninstall

```

Manual Configuration

- 📦 Extract the archive
- 🗃 Locate somewhere in your machine
- Copy the relative path that points to the extension/driver
- Open `php.ini`, search for `;extension`, and if you are using nano press ctrl+w before searching
- Add `extension=liblibsql_php.so` on the following line in Linux, without a leading `;`
Check on your console/terminal

```
$ php --m | grep libsql
liblibsql_php

```

 After this, the `LibSQL` class is available in the PHP environment and can be used throughout the PHP project.1

Configure database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credentials into the environment variables declared in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Keep this for later when connecting to **Remote** and **Embedded Replica**.2

Create PHP Project

Create new directory:

```
mkdir your-awesome-php-project
cd your-awesome-php-project

```

3

Install Turso Doctrine DBAL

```
composer require tursodatabase/turso-doctrine-dbal

```

4

Configure database connection

Set up the environment variable in your Laravel application according to the connection type you need.

```
$params = [
    "url"               => ":memory:",
    'driverClass'       => \Turso\Doctrine\DBAL\Driver::class,
];

```

**Additional environment variables for Embedded Replica Only**

Display additional environment variables for Embedded Replica Only

`sync_interval` integer: an Integer value for the synchronization interval in seconds (optional, default: `5`). `read_your_writes` boolean: a Boolean value that controls whether to read your writes (optional, default: `true`). `encryption_key` string: a String value used for encryption (optional, default: empty).5

Project Structure Example

```
src/
├── helpers.php
└── Todo.php
vendor/
composer.json
composer.lock
todo            # todo executable

```

This is a simple PHP todo cli using Doctrine BDAL

```
#!/usr/bin/env php
<?php

use Turso\Todo\CLI\Todo;

require_once __DIR__ . '/vendor/autoload.php';

$header = 'TODO CLI + TURSO';

echo $header . PHP_EOL;
function main()
{
    echo str_repeat('-', WIDTH - 18) . "  Todo CLI - " . VERSION . PHP_EOL;

    while (true) {
        echo "[L] List | [A] Add | [E] Edit | [D] Delete | [X] Exit" . PHP_EOL;
        echo "command: ";

        $choice = trim(fgets(STDIN));

        $app = new Todo();

        switch (strtolower($choice)) {
            case 'l':
                $app->listTodos();
                break;
            case 'a':
                $app->addTodo();
                clearScreen();
                break;
            case 'e':
                $app->editTodo();
                clearScreen();
                break;
            case 'd':
                $app->deleteTodo();
                clearScreen();
                break;
            case 'x':
                exit("Goodbye!" . PHP_EOL);
            default:
                echo "Invalid choice." . PHP_EOL;
                clearScreen();
        }
    }

    echo PHP_EOL;
}

main();

```

6

Your PHP Application Ready!

(Figure: PHP TODO CLI - TURSO)

### Examples

### PHP Todo CLI + Doctrine DBAL

See the full source code

### Symfony Rest API CRUD

See the full source code

## Quickstart

This PHP quickstart covers how to:
- Install libSQL with Composer
- Retrieve database credentials
- Connect to a Turso database
- Execute a query using SQL
- Synchronize changes to a local database when needed (optional)
 1

Retrieve database credentials

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

Store these values as environment variables.2

Install

Use composer to install the package into your project:

```
composer require turso/libsql

```

3

Connect

Next, connect to the local or remote database with the libSQL connector:

Embedded Replica

```
use Libsql\Database;

$db = new Database(
    path: 'test.db',
    url: getenv('TURSO_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN')
    syncInterval: 100 // Sync every second
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

Remote only

```
use Libsql\Database;

$db = new Database(
    url: getenv('TURSO_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
)

```

4

Execute

Run SQL queries against the existing database like this:

```
$createUsers = "
  CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT,
      age INTEGER
  );
  INSERT INTO users (name, age) VALUES ('Prof. Ir. Onno Widodo Purbo, M.Eng., Ph.D', 61);
  INSERT INTO users (name, age) VALUES ('Jim Geovedi', 41);
  INSERT INTO users (name, age) VALUES ('Nasirun', 59);
";

$db->executeBatch($createUsers);
$db->query("SELECT * FROM users")->fetchArray();

```

Use placeholders when values should be supplied separately:

```
$db->query("SELECT * FROM users WHERE id = ?", [1])->fetchArray();

```

5

Sync (Embedded Replicas only)

With embedded replicas, call `sync()` on the database type to synchronize the local database with the primary database:

```
<?php

$db->sync();

```

## Installing

### Installing

 Use composer to add the package to your project:

```
composer require turso/libsql

```

### Initializing

 Add `use Libsql\Database` so the `Database` object is available.

```
$db = new Database("local.db")

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
$db = new Database("local.db")

```

 Or more explicitly:

```
$db = new Database(path: "local.db")

```

### Remote Only

 A remote only database can be used by passing `url` and `authToken`.

```
$db = new Database(
    url: getenv('TURSO_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
);

```

### Embedded Replicas

 Embedded replicas are configured by passing a `path`, `url`, and `authToken`. They can synchronize from the remote URL and delegate writes to the remote primary database:

```
$db = new Database(
    path: 'test.db',
    url: getenv('TURSO_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
);

```

### Sync Interval

 The `sync_interval` function sets an interval for automatic background synchronization of the database:

```
$db = new Database(
    path: 'test.db',
    url: getenv('TURSO_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
    syncInterval: 300, // Sync every 3 seconds
);

```

### Manual Sync

 The `sync` function lets you manually synchronize the local database with the remote counterpart:

```
$db->sync()

```

### Read Your Own Writes

 The `readYourWrites` parameter makes writes immediately visible to later read operations from the same connection. It is **enabled by default** and is especially useful in distributed systems for preserving consistency from the writer's perspective. Pass `false` to disable it:

```
$db = new Database(
    path: 'test.db',
    url: getenv('TURSO_URL'),
    authToken: getenv('TURSO_AUTH_TOKEN'),
    readYourWrites: false,
);

```

### Simple Query

 Get a connection from a database, then call `query()` to run a SQL statement with optional arguments:

```
$rows = $conn->query("SELECT * FROM users");

```

### Prepared Statements

 Create a cached statement with `prepare()`, bind parameters, and query it afterward:

```
$stmt = $conn->prepare("SELECT * FROM users where id = ?");
$rows = $stmt->bind([1])->query();

```

### Placeholders

 libSQL allows both positional and named placeholders in SQL statements:

```
conn->query("SELECT * FROM users WHERE id = ?", [1]);

```

### Batch Transactions

 A batch executes multiple SQL statements sequentially inside an implicit transaction. The backend controls the transaction, committing every change on success and rolling back all modifications if any statement fails.

```
$conn->execute_batch("
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL
  );

  INSERT INTO users (name) VALUES ('Alice');
  INSERT INTO users (name) VALUES ('Bob');
");

```

### Interactive Transactions

 Interactive transactions in SQLite preserve consistency across a sequence of read and write operations within the transaction's scope. They let you choose when to commit or roll back changes while isolating that work from other client activity.

```
$tx = conn->transaction();

$tx->execute("INSERT INTO users (name) VALUES (?1)", ["Iku"]);
$tx->execute("INSERT INTO users (name) VALUES (?1)", ["Iku 2"]);

tx->commit(); // or, $tx->rollback()

```
