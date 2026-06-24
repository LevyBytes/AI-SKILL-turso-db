# Invites, Invoices, List, Members ...

> Platform Api reference. Original prose; identifiers preserved verbatim.

## Overview

Covers: Authorizations.

## Authorizations

```
curl -L -X POST https://api.turso.tech/v2/organizations/{organizationSlug}/invites \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"email": "user@example.com", "role": "member"}'

```

```
{
  "invited": {
    "email": "iku@turso.tech",
    "role": "member",
    "organization": "mycompany"
  }
}
```

POST/v2/organizations/{organizationSlug}/invites

```
curl -L -X POST https://api.turso.tech/v2/organizations/{organizationSlug}/invites \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"email": "user@example.com", "role": "member"}'

```

```
{
  "invited": {
    "email": "iku@turso.tech",
    "role": "member",
    "organization": "mycompany"
  }
}
```

Invitations require the scaler plan or a higher plan. Send an invite using either **email** or **username**, but not both. If you invite by username, Turso delivers the invite to the email registered for that user. Inviting users requires the `owner` or `admin` role. **Users can be invited only to a team, not to your personal account.** A new invite replaces any pending invite for the same email.

```
curl -L -X POST https://api.turso.tech/v2/organizations/{organizationSlug}/invites \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{"email": "user@example.com", "role": "member"}'

```

#### Authorizations

Authorizationstringheaderrequired

Supply Bearer auth using `Bearer <token>`; here, `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug identifying either the organization or the user account.

#### Body

application/json

User details for the Turso invitation and the organization they will join. Include exactly one of email or username.

emailstring

Email address for the invitee. Provide exactly one of email or username.

usernamestring

Username for an existing Turso user to invite. Provide exactly one of email or username.

roleenum<string>default:member

Role assigned to the user.

Available options: `admin`, `member`, `viewer`

#### Response

200 - application/json

Successful response

invitedobject

Show child attributes

## Authorizations

```
curl -L -X DELETE https://api.turso.tech/v2/organizations/{organizationSlug}/invites/{email} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "invite not found"
}
```

DELETE/v2/organizations/{organizationSlug}/invites/{email}

```
curl -L -X DELETE https://api.turso.tech/v2/organizations/{organizationSlug}/invites/{email} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "error": "invite not found"
}
```

Invites require the scaler plan or a higher plan.

```
curl -L -X DELETE https://api.turso.tech/v2/organizations/{organizationSlug}/invites/{email} \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authenticate with a Bearer header formatted as `Bearer <token>`; replace `<token>` with your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization, or for the user account.

emailstringrequired

Email address used for the invited user.

Example:

`"iku@turso.tech"`

#### Response

Successful response (No Content)

## Authorizations

```
curl -L https://api.turso.tech/v2/organizations/{organizationSlug}/invites \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "invites": [
    {
      "id": 1,
      "email": "iku@turso.tech",
      "role": "member",
      "created_at": "2023-01-01T00:00:00Z"
    }
  ]
}
```

GET/v2/organizations/{organizationSlug}/invites

```
curl -L https://api.turso.tech/v2/organizations/{organizationSlug}/invites \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "invites": [
    {
      "id": 1,
      "email": "iku@turso.tech",
      "role": "member",
      "created_at": "2023-01-01T00:00:00Z"
    }
  ]
}
```

```
curl -L https://api.turso.tech/v2/organizations/{organizationSlug}/invites \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Use Bearer authentication in the `Bearer <token>` header form, with `<token>` set to your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization slug, or the matching user account slug.

#### Response

200 - application/json

Successful response

invitesobject[]

Show child attributes

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/invoices \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "invoices": [
    {
      "invoice_number": "LFONTK-00001",
      "amount_due": "10.29",
      "due_date": "2024-01-01T05:00:00+00:00",
      "paid_at": "2024-01-01T05:00:00+00:00",
      "payment_failed_at": "2024-01-01T05:00:00+00:00",
      "invoice_pdf": "https://assets.withorb.com/invoice/..."
    }
  ]
}
```

GET/v1/organizations/{organizationSlug}/invoices

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/invoices \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "invoices": [
    {
      "invoice_number": "LFONTK-00001",
      "amount_due": "10.29",
      "due_date": "2024-01-01T05:00:00+00:00",
      "paid_at": "2024-01-01T05:00:00+00:00",
      "payment_failed_at": "2024-01-01T05:00:00+00:00",
      "invoice_pdf": "https://assets.withorb.com/invoice/..."
    }
  ]
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/invoices \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Pass a Bearer authentication header as `Bearer <token>`; `<token>` must be your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug value for the organization or user account.

#### Query Parameters

typeenum<string>

Invoice type to retrieve.

Available options: `all`, `upcoming`, `issued`

#### Response

200 - application/json

Successful response

invoicesobject[]

Invoices returned for the organization.

Show child attributes

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations \
  -H 'Authorization: Bearer TOKEN'

```

```
[
  {
    "name": "personal",
    "slug": "iku",
    "type": "personal",
    "overages": false,
    "require_mfa": false,
    "blocked_reads": false,
    "blocked_writes": false,
    "plan_id": "developer",
    "plan_timeline": "monthly",
    "platform": "vercel"
  }
]
```

GET/v1/organizations

```
curl -L https://api.turso.tech/v1/organizations \
  -H 'Authorization: Bearer TOKEN'

```

```
[
  {
    "name": "personal",
    "slug": "iku",
    "type": "personal",
    "overages": false,
    "require_mfa": false,
    "blocked_reads": false,
    "blocked_writes": false,
    "plan_id": "developer",
    "plan_timeline": "monthly",
    "platform": "vercel"
  }
]
```

```
curl -L https://api.turso.tech/v1/organizations \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Provide Bearer authentication as `Bearer <token>`, where `<token>` contains your auth token.

#### Response

200 - application/json

Successful response

namestring

Organization name. Each user also has a `personal` organization for that user's account.

Example:

`"personal"`

slugstring

Organization slug. For `personal` accounts, this is your username.

Example:

`"iku"`

typeenum<string>

Account type for this organization. The value is always `personal` or `team`.

Available options: `personal`, `team` Example:

`"personal"`

overagesboolean

Name of the organization. Each user has a `personal` organization attached to their own account.

Example:

`false`

require_mfaboolean

Indicates whether every organization member must enable multi-factor authentication.

Example:

`false`

blocked_readsboolean

Current blocked reads status.

Example:

`false`

blocked_writesboolean

Current blocked writes status.

Example:

`false`

plan_idstring

Pricing plan identifier for the organization subscription.

Example:

`"developer"`

plan_timelinestring

Billing cycle for any paid plan.

Example:

`"monthly"`

platformstring

External platform managing the organization. This is empty for Turso managed organizations.

Example:

`"vercel"`

## Authorizations

Add Member

```
curl --request POST \
  --url https://api.turso.tech/v1/organizations/{organizationSlug}/members \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '
{
  "username": "<string>",
  "role": "member"
}
'
```

```
{
  "member": "<unknown>",
  "role": "<unknown>"
}
```

POST/v1/organizations/{organizationSlug}/membersAdd Member

```
curl --request POST \
  --url https://api.turso.tech/v1/organizations/{organizationSlug}/members \
  --header 'Authorization: Bearer <token>' \
  --header 'Content-Type: application/json' \
  --data '
{
  "username": "<string>",
  "role": "member"
}
'
```

```
{
  "member": "<unknown>",
  "role": "<unknown>"
}
```

To add someone who has not registered with Turso, use [create an invite](https://docs.turso.tech/api-reference/organizations/invites/create) instead. Adding members requires the `owner` or `admin` role. **Users can be added only to a team, not to your personal account.**

#### Authorizations

Authorizationstringheaderrequired

Set the Bearer authorization header to `Bearer <token>` and use your auth token for `<token>`.

#### Path Parameters

organizationSlugstringrequired

Slug identifying the organization, or a user account slug.

#### Body

application/json

Member record to add.

usernamestring

Username for an existing Turso user.

roleenum<string>default:member

Role assigned to the user.

Available options: `admin`, `member`, `viewer`

#### Response

Successful response

memberanyroleany

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/members \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "members": [
    {
      "username": "iku",
      "role": "owner",
      "email": "iku@turso.tech"
    }
  ]
}
```

GET/v1/organizations/{organizationSlug}/members

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/members \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "members": [
    {
      "username": "iku",
      "role": "owner",
      "email": "iku@turso.tech"
    }
  ]
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/members \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authorize the request with `Bearer <token>` as the Bearer header value; `<token>` is your auth token.

#### Path Parameters

organizationSlugstringrequired

Organization slug or user account slug.

#### Response

200 - application/json

Successful response

membersobject[]

Show child attributes

## Authorizations

```
curl -L -X DELETE https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "member": "<unknown>"
}
```

DELETE/v1/organizations/{organizationSlug}/members/{username}

```
curl -L -X DELETE https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "member": "<unknown>"
}
```

```
curl -L -X DELETE https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Include a Bearer auth header in the `Bearer <token>` shape, using your auth token as `<token>`.

#### Path Parameters

organizationSlugstringrequired

Slug for the target organization or user account.

usernamestringrequired

Username for either a Turso user or an organization member.

#### Response

Successful response

memberany

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "member": {
    "username": "iku",
    "role": "owner",
    "email": "iku@turso.tech"
  }
}
```

GET/v1/organizations/{organizationSlug}/members/{username}

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "member": {
    "username": "iku",
    "role": "owner",
    "email": "iku@turso.tech"
  }
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Send Bearer credentials as `Bearer <token>`; substitute your auth token for `<token>`.

#### Path Parameters

organizationSlugstringrequired

Organization or user account slug.

usernamestringrequired

Username identifying a Turso user or organization member.

#### Response

Successful response

memberobject

Show child attributes

## Authorizations

```
curl -L -X PATCH https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "role": "member"
  }'

```

```
{
  "member": {
    "username": "<string>",
    "email": "<string>"
  }
}
```

PATCH/v1/organizations/{organizationSlug}/members/{username}

```
curl -L -X PATCH https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "role": "member"
  }'

```

```
{
  "member": {
    "username": "<string>",
    "email": "<string>"
  }
}
```

```
curl -L -X PATCH https://api.turso.tech/v1/organizations/{organizationSlug}/members/{username} \
  -H 'Authorization: Bearer TOKEN' \
  -H 'Content-Type: application/json' \
  -d '{
    "role": "member"
  }'

```

#### Authorizations

Authorizationstringheaderrequired

Use the Bearer header value `Bearer <token>` and place your auth token in `<token>`.

#### Path Parameters

organizationSlugstringrequired

Slug of the relevant organization or user account.

usernamestringrequired

Username of the Turso user or organization member.

#### Body

application/jsonroleenum<string>required

New role to assign to the member.

Available options: `admin`, `member`, `viewer`

#### Response

Successful response

memberobject

Show child attributes

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/plans \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "plans": [
    {
      "name": "starter",
      "price": "0",
      "prices": [
        {
          "value": "29",
          "timeline": "monthly"
        }
      ],
      "quotas": {
        "rowsRead": 1000000000,
        "rowsWritten": 25000000,
        "databases": 500,
        "locations": 3,
        "storage": 9000000000,
        "groups": 1,
        "bytesSynced": 3000000000
      }
    }
  ]
}
```

GET/v1/organizations/{organizationSlug}/plans

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/plans \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "plans": [
    {
      "name": "starter",
      "price": "0",
      "prices": [
        {
          "value": "29",
          "timeline": "monthly"
        }
      ],
      "quotas": {
        "rowsRead": 1000000000,
        "rowsWritten": 25000000,
        "databases": 500,
        "locations": 3,
        "storage": 9000000000,
        "groups": 1,
        "bytesSynced": 3000000000
      }
    }
  ]
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/plans \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Authenticate by sending `Bearer <token>` as the Bearer value, with `<token>` containing your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug for the organization or user account being queried.

#### Response

200 - application/json

Successful response

plansobject[]

List of available plans.

Show child attributes

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "organization": {
    "name": "personal",
    "slug": "iku",
    "type": "personal",
    "overages": false,
    "require_mfa": false,
    "blocked_reads": false,
    "blocked_writes": false,
    "plan_id": "developer",
    "plan_timeline": "monthly",
    "platform": "vercel"
  }
}
```

GET/v1/organizations/{organizationSlug}

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug} \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "organization": {
    "name": "personal",
    "slug": "iku",
    "type": "personal",
    "overages": false,
    "require_mfa": false,
    "blocked_reads": false,
    "blocked_writes": false,
    "plan_id": "developer",
    "plan_timeline": "monthly",
    "platform": "vercel"
  }
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug} \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

For authorization, send `Bearer <token>` and set `<token>` to your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug of the organization or user account being updated.

#### Response

Successful response

organizationobject

Show child attributes

## Authorizations

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/subscription \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "subscription": {
    "name": "scaler",
    "overages": true,
    "plan": "scaler",
    "timeline": "monthly"
  }
}
```

GET/v1/organizations/{organizationSlug}/subscription

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/subscription \
  -H 'Authorization: Bearer TOKEN'

```

```
{
  "subscription": {
    "name": "scaler",
    "overages": true,
    "plan": "scaler",
    "timeline": "monthly"
  }
}
```

```
curl -L https://api.turso.tech/v1/organizations/{organizationSlug}/subscription \
  -H 'Authorization: Bearer TOKEN'

```

#### Authorizations

Authorizationstringheaderrequired

Use `Bearer <token>` for Bearer authentication; `<token>` represents your auth token.

#### Path Parameters

organizationSlugstringrequired

Slug that identifies the organization or user account.

#### Response

200 - application/json

Successful response

subscriptionobject

Show child attributes
