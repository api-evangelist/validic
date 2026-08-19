---
name: validic-get-developer-credentials
description: >-
  Register a free Validic developer account and provision an organization, so
  you hold the ORG_ID and ORG_TOKEN every other Validic call needs. Includes
  the terms Validic requires you to present to the human BEFORE registering.
api: Validic Developer Signup API
generated: '2026-08-15'
method: searched
source: https://dashboard.validic.com/validic-developer-signup.txt
operations: []
---

# Get Validic developer credentials

Validic runs a free, self-serve developer tier. This is a separate API on a
separate host from Inform.

**Base:** `https://api.dashboard.validic.com`
**Content-Type:** `application/json` on every request
**Human UI:** https://dashboard.validic.com/register

> There is no published OpenAPI for this API. The endpoints, payloads and
> status codes below are transcribed from Validic's own agent guide at
> https://dashboard.validic.com/validic-developer-signup.txt.

## STOP — present the terms first

Validic instructs the agent to show these to the developer and **wait for
explicit acceptance** before calling `/auth/register`:

- No-fee developer access — **no SLA, no uptime guarantee**
- Validic may modify or suspend developer access at any time without notice
- Accounts are pre-populated with **synthetic test data only**
- Developer credentials **must not** be used in production or with real end users
- Full agreement: https://validic.com/online-service-agreement-inform/

**Do not call `/register` until accepted.** This is Validic's instruction, not a
convention — respect it.

## Step 1 — Register

```
POST /auth/register
{ "email": "you@example.com", "password": "..." }

201 { "message": "Verification email sent", "user_sub": "sub-123" }
409 USER_EXISTS | 400 INVALID_PASSWORD | 400 VALIDATION_ERROR
```

Password: at least 8 characters, with uppercase, lowercase, a number and a
special character.

## Step 2 — Verify the email

```
POST /auth/verify
{ "email": "you@example.com", "code": "123456" }

200 { "message": "Email verified" }
400 INVALID_CODE | 400 EXPIRED_CODE | 400 ALREADY_CONFIRMED
```

**A human has to fetch the 6-digit code from their inbox.** You cannot complete
this step unattended. Ask for it and wait.

## Step 3 — Log in

```
POST /auth/login
{ "email": "...", "password": "..." }

200 { "id_token": "...", "access_token": "...", "refresh_token": "..." }
401 INVALID_CREDENTIALS
```

Keep `id_token` — it is the `Authorization` header value for the remaining
signup calls. (Bearer-style value, no `Bearer ` prefix in Validic's example.)

## Step 4 — Provision an organization

```
POST /v1/registrations/provision
Authorization: <id_token>
{}

202 { "status": "provisioning" }                                              -> poll
200 { "status": "provisioned", "organization_id": "...", "organization_token": "..." }
404 REGISTRATION_NOT_FOUND   -> email not verified yet
409 INVALID_REGISTRATION_STATE
```

## Step 5 — Poll until provisioned

Re-call the same request every **15 seconds**. Typically 15–60s.

- `202 provisioning` → wait and retry
- `200 provisioned` → save `organization_id` and `organization_token`

Give up after roughly 10 attempts (~150s) and surface an error rather than
looping forever.

## Done

`organization_id` is your `ORG_ID`; `organization_token` is your `ORG_TOKEN`.
Both are also visible on https://dashboard.validic.com.

Now go to `validic-connect-a-user-and-read-health-data`. The Inform API is on a
different host (`https://api.v2.validic.com`) and uses a **completely different
auth model** — `?token=ORG_TOKEN` as a query parameter, not the `Authorization`
header you used here. Do not carry the `id_token` across.

## What you get

Fresh accounts are seeded with synthetic records. `summaries` has the best seed
coverage — test against that first, and do not read an empty `data: []` on
another resource as a broken integration.

On-demand synthetic generation (the explorer app's "Generate Data" panel) needs
a `DATAGEN_URL` that is still gated behind an Inform representative.
