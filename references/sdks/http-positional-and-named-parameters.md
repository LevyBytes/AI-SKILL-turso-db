# HTTP positional and named parameters

[Context index](http-api-and-package-selection.md) · [Setup and preceding context](http-authentication-and-pipeline-lifecycle.md) · [Continue](http-transactions-results-and-service-endpoints.md)

#### Parameter binding

 Bound-parameter queries are available in two forms:
1. Positional query parameters are matched by their order in the parameter list and use the `?` prefix. For positional parameters, send the values as an array in `args`.

```
{
  "requests": [
    {
      "type": "execute",
      "stmt": {
        "sql": "SELECT * FROM users WHERE name = ?",
        "args": [
          {
            "type": "text",
            "value": "Turso"
          }
        ]
      }
    },
    {
      "type": "close"
    }
  ]
}

```

1. Named bound parameters are referenced by name and start with `:`, `@`, or `$`. For named parameters, provide `named_args` as an array of objects that map each parameter to its value.

```
{
  "requests": [
    {
      "type": "execute",
      "stmt": {
        "sql": "SELECT * FROM users WHERE name = :name OR name = $second_name OR name = @third_name",
        "named_args": [
          {
            "name": "name",
            "value": {
              "type": "text",
              "value": "Turso"
            }
          },
          {
            "name": "second_name",
            "value": {
              "type": "text",
              "value": "Not Turso"
            }
          },
          {
            "name": "third_name",
            "value": {
              "type": "text",
              "value": "Maybe Turso"
            }
          }
        ]
      }
    },
    {
      "type": "close"
    }
  ]
}

```

 Each `named_args` object's `name` property may include the prefix or leave it off; the example above leaves prefixes out, and both forms work. For each arg, `type` maps to the column datatype and must be one of `null`, `integer`, `float`, `text`, or `blob`. When using `blob`, replace `value` with `base64` and encode the argument as `base64` before sending the request. Integer `value` fields are decimal strings, float values are JSON numbers, and text values are strings. Null arguments have no `value` field.
