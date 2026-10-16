# JSON patching and pretty printing

[Context index](json-function-reference.md) · [Setup and preceding context](json-paths-creation-and-binary-format.md) · [Previous phase](json-replacement-setting-and-removal.md) · [Continue](json-array-length-and-group-aggregation.md)

### json_patch / jsonb_patch

 Applies an RFC 7396 merge patch against a JSON document. Members from the patch object replace matching target members, and a patch member set to `null` deletes the matching member.

```
json_patch(json_text, patch)
jsonb_patch(json_text, patch)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | The target JSON document |
| `patch` | TEXT or BLOB | The merge patch to apply |

 **Returns:** TEXT (json_patch) or BLOB (jsonb_patch) - JSON after patching.

```
SELECT json_patch('{"a":1,"b":2}', '{"b":3,"c":4}');
-- {"a":1,"b":3,"c":4}

-- null in the patch removes a key
SELECT json_patch('{"a":1,"b":2}', '{"b":null}');
-- {"a":1}

```

### json_pretty

 Outputs a JSON document in an indented, human-readable layout.

```
json_pretty(json_text)

```

| Parameter | Type | Description |
| --- | --- | --- |
| `json_text` | TEXT or BLOB | A JSON document |

 **Returns:** TEXT - indented JSON text.

```
SELECT json_pretty('{"name":"Alice","scores":[90,85,92]}');
/*
{
    "name": "Alice",
    "scores": [
        90,
        85,
        92
    ]
}
*/

```

---

### JSON Array Functions
