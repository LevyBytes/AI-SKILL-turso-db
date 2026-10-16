# Sqlalchemy connections and queries

[Context index](python-and-ruby-sdk-workflows.md) · [Setup and preceding context](sqlalchemy-installation-and-model.md)

### Step 4: Create engine

Embedded Replicas

```
import os
from dotenv import load_dotenv
from sqlalchemy import create_engine

load_dotenv()
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
import os
from dotenv import load_dotenv
from sqlalchemy import create_engine

load_dotenv()
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

### Step 5: Query

app.py

```
from sqlalchemy.orm import Session
from sqlalchemy import select
from models import Foo

# Use one engine configuration above; create the foo table first.
with Session(engine) as session:
    for item in session.scalars(select(Foo)):
        print(item)

```
