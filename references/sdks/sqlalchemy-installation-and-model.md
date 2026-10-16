# Sqlalchemy installation and model

[Context index](python-and-ruby-sdk-workflows.md) · [Continue](sqlalchemy-connections-and-queries.md)

## Prerequisites

(Figure: SQLAlchemy Quickstart)

### Prerequisites

 Before beginning, confirm that you have completed these setup items:
- [Set up the Turso CLI](https://docs.turso.tech/cli/installation)
- [Create an account or sign in to Turso](https://docs.turso.tech/cli/authentication#signup)
### Step 1: Add the libSQL dialect package for SQLAlchemy.

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

class Foo(Base):
    __tablename__ = "foo"
    id: Mapped[str] = mapped_column(primary_key=True)
    bar: Mapped[str] = mapped_column(String(100))
    def __repr__(self) -> str:
        return f"Item(id={self.id!r}, bar={self.bar!r})"

```
