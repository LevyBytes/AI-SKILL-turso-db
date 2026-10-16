# Union types tags and extraction

[Context index](custom-types-and-view-reference.md) · [Setup and preceding context](custom-type-base-encoding-and-decoding.md) · [Previous phase](struct-types-packing-and-extraction.md) · [Continue](composite-type-null-and-dot-semantics.md)

### UNION

 A UNION is a discriminated union (tagged variant). Each stored value has exactly one declared variant, selected by a tag. Dot notation extracts the value for a variant and returns NULL when that variant is not active:

```
CREATE TYPE platform_id AS UNION(telegram INT, slack TEXT, signal TEXT);

CREATE TABLE contacts (
    id INTEGER PRIMARY KEY,
    platform platform_id
) STRICT;

INSERT INTO contacts VALUES (1, union_value('telegram', 12345));
INSERT INTO contacts VALUES (2, union_value('slack', 'U0ABC'));
INSERT INTO contacts VALUES (3, union_value('signal', '+1555'));

-- Dot notation extracts the variant value (NULL if tag doesn't match)
SELECT id, platform.telegram, platform.slack FROM contacts ORDER BY id;
-- 1|12345|
-- 2||U0ABC
-- 3||

-- Combine with union_tag() to filter by variant
SELECT id, platform.slack FROM contacts WHERE union_tag(platform) = 'slack';
-- 2|U0ABC

```

 For unions with struct-type variants, chain dot notation to access fields inside the nested struct:

```
CREATE TYPE telegram_msg AS STRUCT(chat_id INT, text TEXT);
CREATE TYPE msg AS UNION(telegram telegram_msg, slack TEXT);
CREATE TABLE messages(id INT, data msg) STRICT;

INSERT INTO messages VALUES (1, union_value('telegram', struct_pack(100, 'hello')));

-- col.variant.field
SELECT data.telegram.chat_id, data.telegram.text FROM messages;
-- 100|hello

```

#### union_value()

 Constructs a union value for INSERT and UPDATE. Pass the variant tag (a string literal) first and the value second. The tag is matched against the target column's union type.

```
INSERT INTO contacts VALUES (1, union_value('telegram', 12345));
UPDATE contacts SET platform = union_value('slack', 'U0XYZ') WHERE id = 1;

```

#### union_tag()

 Returns the active variant's tag name as text.

```
SELECT id, union_tag(platform) FROM contacts ORDER BY id;
-- 1|telegram
-- 2|slack
-- 3|signal

```

#### union_extract()

 Functional equivalent of dot notation: `union_extract(col, 'variant')` matches `col.variant`. It is mainly useful in expression indexes:

```
CREATE INDEX idx_tg ON contacts(union_extract(platform, 'telegram'));

```
