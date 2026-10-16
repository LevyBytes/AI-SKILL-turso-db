# Attach permissions and CLI workflow

[Context index](vectors-attach-branches-and-edge-replicas.md) · [Continue](attach-sdk-transactions-and-restrictions.md)

## How it works

This feature is deprecated for all new users; existing paid users may still use `ATTACH` - [read the announcement](https://turso.tech/blog/upcoming-changes-to-the-turso-platform-and-roadmap). `ATTACH` lets one transaction link multiple databases, making it useful for:
- Keeping data organized modularly
- Improving scalability while making data access more streamlined
- Aggregating data

### How it works

1. Enable `ATTACH` on the databases that should connect to each other.
2. Get the **Database ID** for the database you plan to `ATTACH`.
3. You connect to the database
  - **CLI**: Use `--attach` so the generated token automatically receives the required permissions.
  - **SDK**: Create a token for the target database with the `attach` permission.

4. Run `ATTACH` from the database shell or SDK to connect the other databases.

### Usage

 Use `ATTACH` to connect other databases inside a transaction from the CLI or a libSQL SDK. After attaching, query each database by its alias as though it belongs to the current database.

### Turso CLI

 Confirm that the [Turso CLI](https://docs.turso.tech/cli/installation) is installed and that you are [logged in](https://docs.turso.tech/cli/auth/login).

### Step 1: Enable attach on required databases

First enable `ATTACH` on the database(s) you intend to attach:

```
turso db config attach allow <database-name>

```

### Step 2: Retrieve Database ID

Next retrieve the **Database ID** for the database you intend to `ATTACH`:

```
turso db show <database-name>

```

### Step 3: Open a connection to any database with attach

When connecting to your database(s), provide the database names through the `--attach` flag:

```
turso db shell <database-name> --attach <...database-name(s)>

```

### Step 4: ATTACH

After the connection is open, invoke an `ATTACH` statement to connect the other database(s):

```
ATTACH "<database-id>" AS my_db;

```

### Step 5: Query

Run a query through the alias assigned to any attached database(s):

```
SELECT * FROM my_db.my_table;

```
