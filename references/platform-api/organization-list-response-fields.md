# Organization list response fields

[Context index](organization-membership-and-billing-api.md) · [Setup and preceding context](list-organizations-example.md)

#### Response

200 - application/json

Successful response

**`name`** — string.

Organization name. Each user also has a `personal` organization for that user's account.

Example:

`"personal"`

**`slug`** — string.

Organization slug. For `personal` accounts, this is your username.

Example:

`"iku"`

**`type`** — enum&lt;string&gt;.

Account type for this organization. The value is always `personal` or `team`.

Available options: `personal`, `team` Example:

`"personal"`

**`overages`** — boolean.

Whether paid overages are enabled for this organization.

Example:

`false`

**`require_mfa`** — boolean.

Indicates whether every organization member must enable multi-factor authentication.

Example:

`false`

**`blocked_reads`** — boolean.

Current blocked reads status.

Example:

`false`

**`blocked_writes`** — boolean.

Current blocked writes status.

Example:

`false`

**`plan_id`** — string.

Pricing plan identifier for the organization subscription.

Example:

`"developer"`

**`plan_timeline`** — string.

Billing cycle for any paid plan.

Example:

`"monthly"`

**`platform`** — string.

External platform managing the organization. This is empty for Turso managed organizations.

Example:

`"vercel"`
