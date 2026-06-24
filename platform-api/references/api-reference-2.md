# Groups, Introduction

> Platform Api reference. Original prose; identifiers preserved verbatim.

## Overview

Includes: Authorizations; Quickstart with the TypeScript SDK.

## Authorizations

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/configuration' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "delete_protection": true
}
```

GET/v1/organizations/{organizationSlug}/groups/{groupName}/configuration

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/configuration' \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "delete_protection": true
}
```

```
curl -L -X GET 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/configuration' \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Send a Bearer auth header formatted as `Bearer <token>`; use `<token>` as the authentication token value.

#### Path Parameters

organizationSlugstringrequired

Slug identifying the organization or user account.

groupNamestringrequired

The name of the group.

#### Response

200 - application/json

Successful response

delete_protectionboolean

Block deletion of the group.

Example:

`true`

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups' \
-H 'Authorization: Bearer TOKEN' \
-H 'Content-Type: application/json' \
-d '{
    "name": "new-group",
    "location": "lhr"
}'

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

POST/v1/organizations/{organizationSlug}/groups

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups' \
-H 'Authorization: Bearer TOKEN' \
-H 'Content-Type: application/json' \
-d '{
    "name": "new-group",
    "location": "lhr"
}'

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

Additional groups can be created only on Scaler, Pro, and Enterprise plans.

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups' \
-H 'Authorization: Bearer TOKEN' \
-H 'Content-Type: application/json' \
-d '{
    "name": "new-group",
    "location": "lhr"
}'

```

#### Authorizations

Authorizationstringheaderrequired

Provide the Bearer authentication header in the `Bearer <token>` format; set `<token>` to your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization or user account slug.

#### Body

application/json

Data used to create a new group.

namestringrequired

Name assigned to the new group.

locationstringrequired

Location key assigned to the new group.

extensionsenum<string>enum<string>[]enum<string>enum<string>[]

Extensions enabled for new databases created in this group. For vector extensions, users should use the native [libSQL vector datatype](https://docs.turso.tech/features/ai-and-embeddings) instead.

Available options: `all`

#### Response

Successful response

groupobject

The newly created group

Show child attributes

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/auth/tokens?expiration=2w&authorization=full-access' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "jwt": "TOKEN"
}

```

POST/v1/organizations/{organizationSlug}/groups/{groupName}/auth/tokens

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/auth/tokens?expiration=2w&authorization=full-access' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "jwt": "TOKEN"
}

```

After creation, tokens are not retrievable and cannot be revoked one at a time.

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/auth/tokens?expiration=2w&authorization=full-access' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "jwt": "TOKEN"
}

```

#### Authorizations

Authorizationstringheaderrequired

Authenticate with a Bearer header written as `Bearer <token>`; replace `<token>` with your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization or user account.

groupNamestringrequired

The name of the group.

#### Query Parameters

expirationstringdefault:never

Set when the token expires, for example 2w1d30m.

authorizationenum<string>default:full-access

Authorization level granted to the token: full-access or read-only.

Options supported: `full-access`, `read-only`

#### Body

application/json

Extra context, such as claims the token must include.

permissionsobject

The permissions for the token.

Show child attributes

#### Response

Successful response

jwtstring

The generated authorization token (JWT).

## Authorizations

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

DELETE/v1/organizations/{organizationSlug}/groups/{groupName}

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

```
curl -L -X DELETE 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}' \
-H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

The request uses a Bearer authentication header: `Bearer <token>`, where `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

The organization slug or user account slug.

groupNamestringrequired

The name of the group.

#### Response

Successful response

groupobject

Show child attributes

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/auth/rotate' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "group not found"
}
```

POST/v1/organizations/{organizationSlug}/groups/{groupName}/auth/rotate

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/auth/rotate' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "group not found"
}
```

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/auth/rotate' \
-H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Include your auth token in a Bearer authentication header formatted `Bearer <token>`; `<token>` is the placeholder for that value.

#### Path Parameters

organizationSlugstringrequired

Slug value for an organization or user account.

groupNamestringrequired

The name of the group.

#### Response

Successful response (No Content)

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/groups \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "groups": [
    {
      "name": "default",
      "version": "v0.23.7",
      "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
      "locations": [
        "aws-us-east-1"
      ],
      "primary": "us-east-1",
      "delete_protection": false
    }
  ]
}
```

GET/v1/organizations/{organizationSlug}/groups

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/groups \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "groups": [
    {
      "name": "default",
      "version": "v0.23.7",
      "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
      "locations": [
        "aws-us-east-1"
      ],
      "primary": "us-east-1",
      "delete_protection": false
    }
  ]
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/groups \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Use the Bearer header value `Bearer <token>` to authenticate; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Identifier slug for the organization or user account.

#### Response

200 - application/json

Successful response

groupsobject[]

Show child attributes

## Authorizations

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

GET/v1/organizations/{organizationSlug}/groups/{groupName}

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}' \
-H 'Authorization: Bearer TOKEN'

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

```
curl -L 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}' \
-H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authorize the call with a Bearer authentication header whose value is `Bearer <token>`; `<token>` holds the auth token.

#### Path Parameters

organizationSlugstringrequired

Slug belonging to the target organization or user account.

groupNamestringrequired

The name of the group.

#### Response

Successful response

groupobject

Show child attributes

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/transfer' \
-H 'Authorization: Bearer TOKEN' \
-d '{
    "organization": "new-organization-slug"
}'

```

```
{
  "name": "default",
  "version": "v0.23.7",
  "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
  "locations": [
    "aws-us-east-1"
  ],
  "primary": "us-east-1",
  "delete_protection": false
}
```

POST/v1/organizations/{organizationSlug}/groups/{groupName}/transfer

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/transfer' \
-H 'Authorization: Bearer TOKEN' \
-d '{
    "organization": "new-organization-slug"
}'

```

```
{
  "name": "default",
  "version": "v0.23.7",
  "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
  "locations": [
    "aws-us-east-1"
  ],
  "primary": "us-east-1",
  "delete_protection": false
}
```

You may transfer groups only to organizations that you own or where you are an admin. Existing database URL and tokens keep working, but update your application to the new URL and token as soon as you can.

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/transfer' \
-H 'Authorization: Bearer TOKEN' \
-d '{
    "organization": "new-organization-slug"
}'

```

#### Authorizations

Authorizationstringheaderrequired

Pass a Bearer authentication header in the shape `Bearer <token>` and set `<token>` to your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization or user account in the path.

groupNamestringrequired

The name of the group.

#### Body

application/json

Receiving Organization details.

organizationstring

Slug for the organization that will receive the group.

#### Response

Successful response

namestring

Group name, unique within your organization.

Example:

`"default"`

versionstringdeprecated

The libSQL server version currently used by databases in this group.

Example:

`"v0.23.7"`

uuidstring

The UUID that uniquely identifies the group.

Example:

`"0a28102d-6906-11ee-8553-eaa7715aeaf2"`

locationsstring[]deprecated

Array containing the location keys where the group resides.

Example:

```
["aws-us-east-1"]
```

primarystring

The primary location key.

Example:

`"us-east-1"`

delete_protectionboolean

Current delete protection status. When enabled, neither the group nor any of its databases can be deleted.

Example:

`false`

## Authorizations

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/unarchive' \
-H 'Authorization: Bearer TOKEN' \

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

POST/v1/organizations/{organizationSlug}/groups/{groupName}/unarchive

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/unarchive' \
-H 'Authorization: Bearer TOKEN' \

```

```
{
  "group": {
    "name": "default",
    "version": "v0.23.7",
    "uuid": "0a28102d-6906-11ee-8553-eaa7715aeaf2",
    "locations": [
      "aws-us-east-1"
    ],
    "primary": "us-east-1",
    "delete_protection": false
  }
}
```

On the free plan, databases are archived after 10 days without activity. The API can unarchive inactive groups.

```
curl -L -X POST 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/unarchive' \
-H 'Authorization: Bearer TOKEN' \

```

#### Authorizations

Authorizationstringheaderrequired

For authorization, send a Bearer authentication header with `Bearer <token>` as its value; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization or user account slug for this request.

groupNamestringrequired

The name of the group.

#### Response

Successful response

groupobject

The group that was unarchived

Show child attributes

## Authorizations

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "delete_protection": true
  }'

```

```
{
  "delete_protection": true
}
```

PATCH/v1/organizations/{organizationSlug}/groups/{groupName}/configuration

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "delete_protection": true
  }'

```

```
{
  "delete_protection": true
}
```

```
curl -L -X PATCH 'https://api.turso.tech/v1/organizations/{organizationSlug}/groups/{groupName}/configuration' \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
      "delete_protection": true
  }'

```

#### Authorizations

Authorizationstringheaderrequired

Set the Bearer authentication header to `Bearer <token>` using your auth token as `<token>`.

#### Path Parameters

organizationSlugstringrequired

Slug for the selected organization or user account.

groupNamestringrequired

The name of the group.

#### Body

application/json

Configuration that will be applied to the selected group.

delete_protectionboolean

Keep the group from being deleted.

Example:

`true`

#### Response

200 - application/json

Successful response

delete_protectionboolean

Indicates whether group deletion is blocked.

Example:

`true`

## Quickstart with the TypeScript SDK

### Quickstart with the TypeScript SDK

 For JavaScript or TypeScript, the quickest path to the Platform API is the official [`@tursodatabase/api`](https://www.npmjs.com/package/@tursodatabase/api) client:

```
npm install @tursodatabase/api

```

```
import { createClient } from "@tursodatabase/api";

const turso = createClient({
  org: process.env.TURSO_ORG!,
  token: process.env.TURSO_PLATFORM_TOKEN!,
});

// Create a database
const db = await turso.databases.create("user-abc123", { group: "default" });

// Generate a scoped auth token for that database
const { jwt } = await turso.databases.createToken("user-abc123");

// Delete it when no longer needed
await turso.databases.delete("user-abc123");

```

 See the resource endpoints below for the raw HTTP reference.

### Common use cases

### Database per user / database per agent (multi-tenant)

 Create a separate database for every user or AI agent. Each entity has an isolated SQLite database that you create through the API, token-scope, and later tear down.

```
import { createClient } from "@tursodatabase/api";

const turso = createClient({
  org: process.env.TURSO_ORG!,
  token: process.env.TURSO_PLATFORM_TOKEN!,
});

// On user signup or agent creation — provision a database
const db = await turso.databases.create(`agent-${agentId}`, { group: "default" });

// Generate a scoped token for that database
const { jwt } = await turso.databases.createToken(`agent-${agentId}`);

// Store db.hostname and jwt for the session

```

 After creating the database, connect from your application:
- **Over the wire** - run remote queries with [`@tursodatabase/serverless`](https://docs.turso.tech/sdk/ts/quickstart)
- **Local-first with sync** - synchronize between the database and a local file with [`@tursodatabase/sync`](https://docs.turso.tech/sync/usage)

### Platform / reseller

 Manage and create databases for your end users. The Platform API lets you build a database-as-a-service offering on Turso.

```
// List all databases in your organization
const databases = await turso.databases.list();

// Get details for a specific database
const db = await turso.databases.get("customer-db");

```

### API Quickstart

A step-by-step walkthrough for creating your first database through the API.

### API Resources

 Begin connecting your platform to the Turso API with a few basic steps.

### Groups API

Create and manage where database groups are located.

### Database API

Create databases, manage them, and issue auth tokens for databases.

### Locations API

Retrieve every available location where a database group may reside.

### Organizations API

Handle organization settings, members, and invites.

### API Tokens API

Create, validate, and revoke API keys used to access the API.

### Audit Logs API

Monitor organization activity logs.
