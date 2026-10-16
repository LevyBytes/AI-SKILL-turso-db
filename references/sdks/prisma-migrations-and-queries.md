# Prisma migrations and queries

[Context index](typescript-orm-and-client-quickstarts.md) · [Setup and preceding context](prisma-adapter-and-schema-setup.md)

### Step 6: Database Migrations

Turso does not currently support Prisma Migrate or Introspection workflows; [learn more](https://www.prisma.io/docs/orm/overview/databases/turso#how-to-manage-schema-changes).Start by creating a migration file with prisma migrate dev against a local SQLite database

```
npx prisma migrate dev --name init

```

After that, use the Turso CLI to apply the migration to your Turso database

```
turso db shell turso-prisma-db < ./prisma/migrations/20230922132717_init/migration.sql

```

Change `20230922132717_init` to the migration name produced by the `npx prisma migrate dev` command.

### Step 7: Query

```
const response = await prisma.table_name.findMany();

```
