# Python, Ruby

> Sdks reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: examples; Prerequisites; Recommended: pyturso (Local / Embedded); pyturso; Examples; Quickstart.

## examples

[tursodatabase](https://docs.turso.tech/tursodatabase)  / ** [libsql-python](https://docs.turso.tech/tursodatabase/libsql-python) ** Public
-  [Notifications](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql-python) Sign in before changing notification preferences
-  [Fork count 28](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql-python)
-   [Star count 204](https://docs.turso.tech/login?return_to=%2Ftursodatabase%2Flibsql-python)

### Expand file tree

/

### examples

/Copy path

### Directory actions

### More options

More options

### Directory actions

### More options

More options

### Latest commit

### History

[History](https://docs.turso.tech/tursodatabase/libsql-python/commits/main/examples)History/

### examples

/Copy path

### Folders and files

| Name | Name | Last commit message | Last commit date |
| --- | --- | --- | --- |
| ### parent directory.. |  |  |  |
| batch | batch |  |  |
| encryption | encryption |  |  |
| local | local |  |  |
| memory | memory |  |  |
| remote | remote |  |  |
| sqlalchemy | sqlalchemy |  |  |
| sync | sync |  |  |
| transaction | transaction |  |  |
| vector | vector |  |  |
| execute_script.py | execute_script.py |  |  |
| remote_connect.py | remote_connect.py |  |  |
| statements.sql | statements.sql |  |  |
| sync_write.py | sync_write.py |  |  |
| vector.py | vector.py |  |  |

## Prerequisites

(Figure: Flask banner)

### Prerequisites

 Before beginning, confirm that you have completed these setup items:
- [Set up the Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Flask app available - [learn more](https://flask.palletsprojects.com/en/3.0.x/installation/#create-an-environment)
 1

Install the libSQL dialect

```
pip install sqlalchemy-libsql python-dotenv

```

2

Retrieve database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credential values into the environment variables defined in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

3

Create database models

models.py

```
from sqlalchemy import String
from sqlalchemy.orm import DeclarativeBase
from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column

class Base(DeclarativeBase):
    pass

class Item(Base):
    __tablename__ = "items"
    id: Mapped[str] = mapped_column(primary_key=True)
    foo: Mapped[str] = mapped_column(String(255))
    bar: Mapped[str] = mapped_column(String(100))
    def __repr__(self) -> str:
        return f"Item(id={self.id!r}, foo={self.foo!r}, bar={self.bar!r})"

```

4

Query

```
from dotenv import load_dotenv
from sqlalchemy import create_engine
from sqlalchemy.orm import Session
from sqlalchemy import select
from models import Item

load_dotenv()

# Get environment variables
TURSO_DATABASE_URL = os.environ.get("TURSO_DATABASE_URL")
TURSO_AUTH_TOKEN = os.environ.get("TURSO_AUTH_TOKEN")

# construct special SQLAlchemy URL
dbUrl = f"sqlite+{TURSO_DATABASE_URL}/?authToken={TURSO_AUTH_TOKEN}&secure=true"

engine = create_engine(dbUrl, connect_args={'check_same_thread': False}, echo=True)

@app.route("/", methods=(["GET"]))
def home():
    session = Session(engine)

    # get & print items
    stmt = select(Item)

    for item in session.scalars(stmt):
        print(item)

```

### Examples

### Social App

See the full source code

## Prerequisites

(Figure: SQLAlchemy Quickstart)

### Prerequisites

 Before beginning, confirm that you have completed these setup items:
- [Set up the Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
 1

Add the libSQL dialect package for SQLAlchemy.

```
pip install sqlalchemy-libsql

```

2

Retrieve database credentials

Get the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credential values into the environment variables defined in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

3

Create database models

models.py

```
from sqlalchemy import String
from sqlalchemy.orm import DeclarativeBase
from sqlalchemy.orm import Mapped
from sqlalchemy.orm import mapped_column

class Base(DeclarativeBase):
    pass

class Foo(Base):
    __tablename__ = "foo"
    id: Mapped[str] = mapped_column(primary_key=True)
    bar: Mapped[str] = mapped_column(String(100))
    def __repr__(self) -> str:
        return f"Item(id={self.id!r}, bar={self.bar!r})"

```

4

Create engine

Embedded Replicas

```
from dotenv import load_dotenv
from sqlalchemy import create_engine

TURSO_DATABASE_URL = os.environ.get("TURSO_DATABASE_URL")
TURSO_AUTH_TOKEN = os.environ.get("TURSO_AUTH_TOKEN")

engine = create_engine(
     "sqlite+libsql:///embedded.db",
     connect_args={
         "auth_token": TURSO_AUTH_TOKEN,
         "sync_url": TURSO_DATABASE_URL,
     },
)

```

Remote only

```
from dotenv import load_dotenv
from sqlalchemy import create_engine

TURSO_DATABASE_URL = os.environ.get("TURSO_DATABASE_URL")
TURSO_AUTH_TOKEN = os.environ.get("TURSO_AUTH_TOKEN")

engine = create_engine(
    f"sqlite+{TURSO_DATABASE_URL}?secure=true",
    connect_args={
        "auth_token": TURSO_AUTH_TOKEN,
    },
)

```

Memory Only

```
from sqlalchemy import create_engine

engine = create_engine("sqlite+libsql://")

```

Local only

```
from sqlalchemy import create_engine

engine = create_engine("sqlite+libsql:///local.db")

```

5

Query

app.py

```
from sqlalchemy.orm import Session
from sqlalchemy import select
from models import Foo

@app.route("/", methods=(["GET"]))
def home():
    session = Session(engine)

    # get & print foos
    stmt = select(Foo)

    for item in session.scalars(stmt):
        print(item)

```

## Recommended: pyturso (Local / Embedded)

This Python quickstart covers these tasks:
- Install the Turso package
- Open a connection to a local or remote database
- Execute a query using SQL
- Sync changes to local database

### Recommended: pyturso (Local / Embedded)

 `pyturso` is the preferred package for local and embedded scenarios. It uses the Turso Database engine, a fresh SQLite-compatible implementation with concurrent writes (MVCC) and async I/O. Its interface mirrors Python's standard `sqlite3` API closely enough to act as a drop-in substitute. 1

Install

```
uv add pyturso

```

Or with pip:

```
pip install pyturso

```

2

Connect

```
import turso

db = turso.connect("app.db")

```

You can also open in-memory databases:

```
db = turso.connect(":memory:")

```

3

Execute

```
db.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")
db.execute("INSERT INTO users (name) VALUES (?)", ("Alice",))
db.commit()

for row in db.execute("SELECT * FROM users"):
    print(row)

```

4

Sync (push and pull)

To synchronize a local database with Turso Cloud, use `turso.sync`:

```
import os
import turso.sync

db = turso.sync.connect(
    "app.db",
    remote_url=os.environ["TURSO_DATABASE_URL"],
    auth_token=os.environ["TURSO_AUTH_TOKEN"],
)

db.execute("INSERT INTO users (name) VALUES (?)", ("Bob",))
db.commit()

# Push local writes to Turso Cloud
db.push()

# Pull remote changes to local database
db.pull()

```

Reads and writes use the local database file, so operations stay fast and can work offline. `push()` uploads local changes, while `pull()` retrieves remote changes. See [Turso Sync](https://docs.turso.tech/sync/usage) for conflict resolution, checkpointing, and related details.

### Remote Access (Over-the-Wire)

 When an application must query a Turso Cloud database directly across the network, such as from a web server or serverless function, use the `libsql` package. It connects by HTTP and does not require a local database file. For most applications, Turso recommends a local database with [Turso Sync](https://docs.turso.tech/sync/usage) (`turso.sync`) because reads are faster, offline operation is available, and latency is lower. Direct remote access fits environments that cannot keep a database file locally, including stateless serverless deployments. 1

Retrieve database credentials

You need an existing database before continuing. If one is not available, [create one](https://docs.turso.tech/quickstart). Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credential values into the environment variables defined in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Store these values in environment variables.2

Install

```
uv add libsql

```

Or with pip:

```
pip install libsql

```

3

Connect and query

```
import os
import libsql

conn = libsql.connect(
    database=os.environ["TURSO_DATABASE_URL"],
    auth_token=os.environ["TURSO_AUTH_TOKEN"],
)

conn.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")
conn.execute("INSERT INTO users (name) VALUES (?)", ("Alice",))
conn.commit()

rows = conn.execute("SELECT * FROM users").fetchall()
print(rows)

```

### Embedded Replicas (libsql)

 The `libsql` package can also use Embedded Replicas: reads happen from a local file, writes are forwarded to the cloud primary, and the replica receives the reflected changes. Embedded Replicas are production-supported. For new projects that require sync, Turso recommends `turso.sync`, where reads and writes both occur locally and synchronization is performed explicitly with `push()` / `pull()`. The [reference](https://docs.turso.tech/sdk/python/reference) covers Embedded Replicas, encryption, periodic sync, and related APIs.

## pyturso

Turso offers two Python packages:
|  | `pyturso` | `libsql` |
| --- | --- | --- |
| **Use case** | Local / embedded database, sync | Existing libSQL codebases |
| **Engine** | Turso Database (rewrite) | libSQL (SQLite fork) |
| **Concurrent writes** | Yes (MVCC) | Not supported |
| **Sync** | push/pull (local-first) | Embedded Replicas, with writes forwarded to the cloud primary |
| **API** | Python `sqlite3`-compatible | Python `sqlite3`-compatible |

 **Starting a new project?** Choose `pyturso`; it runs on the Turso Database engine and provides concurrent writes plus local-first sync.

### pyturso

 Use this for local and embedded applications. It runs on the Turso Database engine and supports concurrent writes (MVCC) plus async I/O.

### Installing

```
pip install pyturso

```

### Connecting

```
import turso

conn = turso.connect("app.db")

```

 In-memory databases are supported as well:

```
conn = turso.connect(":memory:")

```

### Querying

```
conn.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)")
conn.execute("INSERT INTO users (name) VALUES (?)", ("Alice",))
conn.commit()

for row in conn.execute("SELECT * FROM users"):
    print(row)

```

### Encryption

 Use the `encryption` option to encrypt local databases at rest:

```
from turso import connect, EncryptionOpts

conn = connect("encrypted.db",
               experimental_features="encryption",
               encryption=EncryptionOpts(cipher="aegis256",
                                         hexkey="b1bbfda4f589dc9daaf004fe21111e00dc00c98237102f5c7002a5669fc76327"))

```

 Supported ciphers are `aegis256`, `aegis256x2`, `aegis128l`, `aegis128x2`, `aegis128x4`, `aes256gcm`, and `aes128gcm`. A standard SQLite reader cannot open encrypted databases; open them with the Turso Database engine instead. Turso Cloud databases also support bring-your-own-key encryption - [learn more](https://docs.turso.tech/cloud/encryption).

### libsql (libSQL)

 The `libsql` package is based on [libSQL](https://github.com/tursodatabase/libsql), the open-source SQLite fork that currently underpins Turso Cloud. It is production-ready and well tested, and it fits existing codebases that already depend on `libsql`. With `libsql` Embedded Replicas, reads use the local replica while writes go to the cloud primary and are then reflected back. Embedded Replicas are fully supported. For new sync-oriented projects, Turso recommends `turso.sync`; see the [quickstart](https://docs.turso.tech/sdk/python/quickstart).

### Embedded Replicas

 For workloads requiring **offline writes**, **bidirectional sync**, or **multi-writer convergence**, Turso recommends `turso.sync`: reads and writes both happen locally, and you synchronize deliberately with `push()` / `pull()`. See the [quickstart](https://docs.turso.tech/sdk/python/quickstart). You can also use [embedded replicas](https://docs.turso.tech/features/embedded-replicas), which sync a remote database into a local SQLite file and send writes to the remote primary database:

```
import os

import libsql

conn = libsql.connect("local.db", sync_url=os.getenv("LIBSQL_URL"),
                      auth_token=os.getenv("LIBSQL_AUTH_TOKEN"))
conn.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER);")
conn.execute("INSERT INTO users(id) VALUES (1);")
conn.commit()

print(conn.execute("select * from users").fetchall())

```

 Embedded Replicas require file-system access.

### Periodic Sync

 Pass seconds to `sync_interval` to synchronize automatically on a schedule. For example, this code syncs once per minute:

```
conn = libsql.connect("local.db", sync_interval=60, sync_url=os.getenv("LIBSQL_URL"),
                      auth_token=os.getenv("LIBSQL_AUTH_TOKEN"))

```

### Manual Sync

 Use the `Sync` function to manually synchronize the local database with its remote counterpart:

```
conn.execute("INSERT INTO users(id) VALUES (2);")
conn.commit()
conn.sync()

```

### Encryption

 For new projects, Turso recommends `pyturso` for local encryption because it uses the Turso Database engine and offers stronger performance plus concurrent-write support. To encrypt a SQLite file, provide the secret through the `encryption_key` option:

```
conn = libsql.connect("encrypted.db", sync_url=os.getenv("LIBSQL_URL"),
                      auth_token=os.getenv("LIBSQL_AUTH_TOKEN"),
                      encryption_key=os.getenv("ENCRYPTION_KEY"))

```

 Encrypted databases look like raw bytes and cannot be opened as normal SQLite databases. Use the libSQL client for operations - [learn more](https://docs.turso.tech/libsql#encryption-at-rest).

## Examples

| Example | Description |
| --- | --- |
| local | Uses libsql with a local SQLite file, then creates a database, inserts rows, and runs queries. |
| remote | Connects to a remote database and expects environment variables for the URL and auth token. |
| sync | Shows synchronization between a local database and a remote database. |
| transactions | Covers transaction flow, including begin, operations, commit, and rollback. |
| memory | Uses an in-memory SQLite database for temporary data or fast access. |
| vector | Stores vector embeddings and queries them for similarity search. |
| encryption | Creates an encrypted SQLite database and demonstrates setup plus data operations. |

## Quickstart

This Ruby quickstart is currently in technical preview. [Join us in Discord](https://discord.gg/turso) to report issues. It covers these tasks:
- Retrieve database credentials
- Install the libSQL package
- Connect to either a local Turso database or a remote Turso database
- Execute a query using SQL
- Optionally sync changes into a local database
 1

Retrieve database credentials

You need an existing database before continuing. If one is not available, [create one](https://docs.turso.tech/quickstart). Then retrieve the database URL:

```
turso db show --url <database-name>

```

Get the database authentication token:

```
turso db tokens create <database-name>

```

Put the credential values into the environment variables defined in `.env`.

```
TURSO_DATABASE_URL=
TURSO_AUTH_TOKEN=

```

Make sure database tokens are handled securely.2

Install

Install this Rubygem inside your Ruby project:

```
bundle add turso_libsql

```

3

Connect

Embedded Replicas

```
require 'turso_libsql'

db =Libsql::Database.new(
  path: 'local.db',
  url: ENV['TURSO_DATABASE_URL'],
  auth_token: ENV['TURSO_AUTH_TOKEN'],
  sync_interval: 100
)

```

Local only

```
require 'turso_libsql'

db = Libsql::Database.new(path: 'local.db')

```

Remote only

```
require 'turso_libsql'

db = Libsql::Database.new(
  url: ENV['TURSO_DATABASE_URL'],
  auth_token: ENV['TURSO_AUTH_TOKEN']
)

```

4

Execute

To run SQL against the existing database, prepare a statement and execute it:

```
db.connect do |conn|
  rows = conn.query 'SELECT * FROM users'
  rows.close
end

```

Placeholders can be used for values when needed:

```
name = 'Iku'
rows = conn.execute 'INSERT INTO users (id) VALUES (?)', [name]

```

5

Sync (Embedded Replicas only)

When using embedded replicas, call `sync` on the database to synchronize the local database with the primary database unless `sync_interval` is already configured. Calling `sync` while `sync_interval` is enabled is also acceptable:

```
db.sync

```
