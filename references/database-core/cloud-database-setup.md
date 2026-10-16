# Cloud database setup

[Context index](database-products-and-cloud-setup.md) · [Continue](cloud-database-shell-and-application-connections.md)

## TypeScript

**This quickstart walks through how to:**
1. Install the Turso CLI
2. Signup to Turso Cloud
3. Set up your first Turso Cloud database
4. Connect to Database Shell
### Step 1: Install the Turso CLI

This quickstart uses the Turso CLI to manage databases and open the database shell.

```
brew install tursodatabase/tap/turso

```

### Step 2: Signup to Turso Cloud

Run the next command to open sign-up in your browser:

```
turso auth signup

```

### Step 3: Create a Database

Create the first database and name it `my-db`:

```
turso db create my-db

```

Inspect the database you just created with this command:

```
turso db show my-db

```
