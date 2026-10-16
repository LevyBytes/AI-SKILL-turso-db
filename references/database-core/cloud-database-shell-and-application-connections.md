# Cloud database shell and application connections

[Context index](database-products-and-cloud-setup.md) · [Setup and preceding context](cloud-database-setup.md)

### Step 4: Connect to Database Shell

After the database exists, connect with the `shell` command:

```
turso db shell my-db

```

If you used another database name, substitute it for `my-db`.

Create table

Create a SQL table named `users`:

```
CREATE TABLE users (
  ID INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT
);

```

Insert into new table

Next, add one row to the `users` table:

```
INSERT INTO users (name) VALUES ('Iku');

```

Select all rows from table

Finally, query for all `users`:

```
SELECT * FROM users;

```

When you are ready for the next step, exit the shell:

```
.quit

```

### Step 5: Connect the application to the database

Your application is ready to connect to the database. Choose one SDK below to continue:

### TypeScript

### Rust

### Go

### Python

### Android

### Swift

### PHP

### Ruby

### Ruby on Rails

### C

### SQL over HTTP
