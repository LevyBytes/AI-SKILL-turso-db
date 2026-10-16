# JavaScript local database setup

## Quickstart

### Step 1: Install

Add the Turso database package to your JavaScript project:

```
npm i @tursodatabase/database

```

### Step 2: Connect

Connect to a local SQLite database with this pattern:

```
import { connect } from "@tursodatabase/database";

const db = await connect("sqlite.db");

```

### Step 3: Create table

Create a table for users:

```
const createTable = await db.prepare(`
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL
  )
`);
await createTable.run();

```

### Step 4: Insert data

Insert rows into the users table:

```
const insertUser = await db.prepare("INSERT INTO users (username) VALUES (?)");
await insertUser.run("alice");
await insertUser.run("bob");

```
