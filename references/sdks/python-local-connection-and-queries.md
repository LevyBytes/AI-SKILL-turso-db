# Python local connection and queries

[Context index](client-connection-and-vercel-workflows.md)

## Python

### Step 1: Install

Add the Turso package to your Python project:

```
uv pip install pyturso

```

### Step 2: Connect

Connect to a local SQLite database using code like this:

```
import turso

con = turso.connect("sqlite.db")
cur = con.cursor()

```

### Step 3: Create table

Create a table for users:

```
cur.execute("""
  CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL
  )
""")
con.commit()

```

### Step 4: Insert data

Insert rows into the users table:

```
cur.execute("INSERT INTO users (username) VALUES (?)", ("alice",))
cur.execute("INSERT INTO users (username) VALUES (?)", ("bob",))
con.commit()

```

### Step 5: Query data

Read every user from the table:

```
res = cur.execute("SELECT * FROM users")
users = res.fetchall()
print(users)

```
