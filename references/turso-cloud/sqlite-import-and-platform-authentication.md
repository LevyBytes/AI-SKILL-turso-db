# SQLite import and platform authentication

[Context index](access-durability-encryption-and-migration.md) · [Setup and preceding context](sqlite-migration-wal-preparation.md) · [Continue](sqlite-upload-and-database-tokens.md)

### Using the CLI

 Use the Turso CLI to create a new database from a local SQLite file: 1

Install the Turso CLI

Confirm the [Turso CLI](https://docs.turso.tech/cli/introduction) is installed and that you are authenticated.

### Step 2: Import your SQLite Database

Use the `db import` command to import the existing SQLite database file:

```
turso db import ~/path/to/my-database.db

```

The database name comes from the file name without the .db extension, and the import includes tables, data, and schema.

### Step 3: Connect to your database

You can then connect to the database with the shell:

```
turso db shell <database-name>

```

### Using the Platform API

 The Platform API is another option for migrating an existing SQLite database: 1

Sign up or log in with the Turso CLI

If it is not already installed, [install the Turso CLI](https://docs.turso.tech/cli/installation).

```
turso auth signup

```

### Step 2: Create a new Platform API Token

Next, create a new API Token with the Turso CLI:

```
turso auth api-tokens mint quickstart

```

Save the token somewhere secure. You will need it when creating a database and a database token.

### Step 3: Get your account or organization slug

The Platform API works with either a personal account or an organization. Use the Turso CLI to obtain the `slug` for the account or organization:

```
turso org list

```
