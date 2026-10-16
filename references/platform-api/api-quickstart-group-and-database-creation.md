# API quickstart group and database creation

[Context index](api-quickstart-tokens-and-user.md) · [Setup and preceding context](api-quickstart-authentication-and-locations.md)

`organizationSlug` identifies your organization or personal account.

### Step 5: Create a Group

Every database is placed in a group, and groups may span one or more locations. Start by creating a group with a `name` and primary `location`:

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "default",
      "location": "lhr"
  }'

```

### Step 6: Create a Database

Create the first database inside the group from the previous step:

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/databases' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "name": "my-db",
      "group": "default"
  }'

```

### Step 7: Connect to your database

At this point, the database exists and is distributed across multiple regions; connect with one of the official or experimental SDKs:

### TypeScript

### Rust

### Go

### Python

### Android

### Swift

### PHP

### Ruby

### Ruby on Rails

### C

### SQL over HTTP
