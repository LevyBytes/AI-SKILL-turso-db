# HTTP pipeline response and typed arguments

[Context index](http-api-and-package-selection.md) · [Setup and preceding context](http-client-packages-and-quickstart.md) · [Previous phase](http-database-url-and-pipeline-request.md)

Response

The response is a JSON object whose `results` array holds query output, similar to this:

```
{
  "baton": null,
  "base_url": null,
  "results": [
    {
      "type": "ok",
      "response": {
        "type": "execute",
        "result": {
          "cols": [],
          "rows": [],
          "affected_row_count": 0,
          "last_insert_rowid": null,
          "replication_index": "1"
        }
      }
    },
    {
      "type": "ok",
      "response": {
        "type": "close"
      }
    }
  ]
}

```

Placeholders can be used for values when needed:

```
{
  "type": "execute",
  "stmt": {
    "sql": "SELECT * FROM users WHERE id = ?",
    "args": [
      {
        "type": "integer",
        "value": "1"
      }
    ]
  }
}

```

For each arg, `type` maps to the column datatype and must be one of `null`, `integer`, `float`, `text`, or `blob`. `integer` uses a decimal string in `value` to preserve 64-bit precision; `float` uses a JSON number and `text` a string. A `null` argument has no value field. A `blob` uses a base64 string in `base64` instead of `value`.
