# PHP doctrine credentials and connection

[Context index](kotlin-and-php-sdk-workflows.md) · [Setup and preceding context](php-doctrine-driver-installation.md) · [Continue](php-doctrine-todo-application-example.md)

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

Keep this for later when connecting to **Remote** and **Embedded Replica**.

### Step 2: Create PHP Project

Create new directory:

```
mkdir your-awesome-php-project
cd your-awesome-php-project

```

### Step 3: Install Turso Doctrine DBAL

```
composer require tursodatabase/turso-doctrine-dbal

```

### Step 4: Configure database connection

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
