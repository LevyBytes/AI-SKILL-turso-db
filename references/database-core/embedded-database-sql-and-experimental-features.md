# Embedded database SQL and experimental features

[Context index](embedded-database-mvcc-and-encryption.md) · [Setup and preceding context](embedded-database-installation-and-shell.md)

### Step 3: Execute

At this point, SQL statements can be executed in the interactive shell:

Create a table

Create a table for users:

```
CREATE TABLE users (id INT, username TEXT);

```

Insert data

Add some rows to the users table:

```
INSERT INTO users VALUES (1, 'alice');
INSERT INTO users VALUES (2, 'bob');

```

Query data

Read every user from the table:

```
SELECT * FROM users;

```

This will return:

```
1|alice
2|bob

```

### Experimental Features

 Experimental features can be enabled while starting `tursodb` with flags including `--experimental-encryption`, `--experimental-mvcc`, `--experimental-strict`, and `--experimental-views`. **Note:** These features are not ready for production, so avoid using them with critical data for now. The [Turso manual](https://github.com/tursodatabase/turso) lists the full set of available flags and options.
