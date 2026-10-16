# Flask database connection and route

[Context index](python-and-ruby-sdk-workflows.md) · [Setup and preceding context](flask-dependencies-and-database-model.md)

### Query from Flask

Use `models.py` from [the model setup](flask-dependencies-and-database-model.md); the `items` table must exist before this route is requested. Install Flask with the setup dependencies.

```python
import os
from dotenv import load_dotenv
from flask import Flask, jsonify
from sqlalchemy import create_engine, select
from sqlalchemy.orm import Session
from models import Item

load_dotenv()
app = Flask(__name__)
url = os.environ["TURSO_DATABASE_URL"]
engine = create_engine(
    f"sqlite+{url}?secure=true",
    connect_args={
        "auth_token": os.environ["TURSO_AUTH_TOKEN"],
        "check_same_thread": False,
    },
)

@app.get("/")
def home():
    with Session(engine) as session:
        items = session.scalars(select(Item))
        return jsonify([
            {"id": item.id, "foo": item.foo, "bar": item.bar}
            for item in items
        ])

```

### Examples

### Social App

See the full source code
