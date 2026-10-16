# Shared schema creation with CLI

[Context index](replica-hosting-and-recovery-workflows.md) · [Continue](shared-schema-api-and-migration-restrictions.md)

## How it works

Multi-DB Schemas is deprecated for all new users, while existing paid users may continue using it; see the [read the announcement](https://turso.tech/blog/upcoming-changes-to-the-turso-platform-and-roadmap) page. Turso lets one schema be shared by multiple databases, which supports multi-tenant applications where every tenant has its own database. **Using TypeScript?** For programmatic database provisioning, use the [`@tursodatabase/api`](https://www.npmjs.com/package/@tursodatabase/api) client instead of direct REST API calls. See the [SDK quickstart](https://docs.turso.tech/api-reference/introduction#quickstart-with-the-typescript-sdk). To start a multi-tenant application with Next.js, Drizzle, Clerk, libSQL, and Turso Multi-DB Schemas, try the [Turso Per User Starter](https://github.com/notrab/turso-per-user-starter).

### How it works

1. Create the database that will act as the parent schema database.
2. Create at least one database to serve as a child database.
3. Apply schema changes on the parent database; the child databases receive the new schema automatically.

### Usage

 Parent and child databases can be created and managed through the [Turso CLI](https://docs.turso.tech/cli/db/create) and the [Platform API](https://docs.turso.tech/api-reference/databases/create).

### Turso CLI

 Ensure the [Turso CLI](https://docs.turso.tech/cli/installation) is installed and that you are [logged in](https://docs.turso.tech/cli/auth/login).

### Step 1: Create Parent Database

Create one database with the `--type` flag set to `schema`:

```
turso db create parent-db --type schema

```

### Step 2: Create Schema

Open a shell connection to the database and use `CREATE TABLE`, `CREATE TRIGGER`, `CREATE VIEW`, or other statements needed to setup the schema:

```
turso db shell parent-db

```

### Step 3: Create Child Database(s)

Create one or more child databases, passing `--schema` with the parent database name:

```
turso db create child-db --schema parent-db

```

### Step 4: Apply Additional Schema Changes

After that, additional schema changes can be made on the parent database, and the child databases update automatically:

```
turso db shell parent-db "CREATE TABLE users (id INT PRIMARY KEY, name TEXT);"

```
