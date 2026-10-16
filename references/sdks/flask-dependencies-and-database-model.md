# Flask dependencies and database model

[Context index](python-and-ruby-sdk-workflows.md) · [Continue](flask-database-connection-and-route.md)

## Prerequisites

### Prerequisites

 Before beginning, confirm that you have completed these setup items:
- [Set up the Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
- Have a Flask app available - [learn more](https://flask.palletsprojects.com/en/3.0.x/installation/#create-an-environment)
### Step 1: Install the libSQL dialect

```
pip install sqlalchemy-libsql python-dotenv

```

### Step 2: Retrieve database credentials

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

### Step 3: Create database models

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
