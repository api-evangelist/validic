---
name: validic-connect-a-user-and-read-health-data
description: >-
  Provision a Validic Inform user, send them to the hosted Marketplace to
  connect a wearable or medical device, then read their normalised health
  observations back over REST. The core Validic flow - everything else is a
  variation on it.
api: Validic Inform API
generated: '2026-08-15'
method: searched
source: >-
  https://dashboard.validic.com/inform-quickstart.txt +
  https://developer.validic.com/docs/provision-and-managing-users +
  openapi/_original/validic-inform-data-resources-openapi.json
operations:
  - provision-a-user
  - get-user-profile
  - get-all-user-profiles
  - generate-new-marketplace-token
  - summaries
  - measurements
  - workouts
  - sleep
  - nutrition
  - intraday-2
  - cgm
---

# Connect a user and read their health data

Validic normalises data from 700+ wearables and in-home medical devices into one
record shape. This skill takes you from "no user" to "reading their steps".

**Base:** `https://api.v2.validic.com`
**Auth:** `?token=ORG_TOKEN` on every request — a query parameter, not a header.
You need `ORG_ID` and `ORG_TOKEN` from https://dashboard.validic.com.

Treat the token like a password. It appears in URLs, in logs, and in every curl
command you copy.

## Step 1 — Provision the user

`provision-a-user` — `POST /organizations/{ORG_ID}/users?token={ORG_TOKEN}`

```json
{ "uid": "your_id", "location": { "timezone": "America/New_York", "country_code": "US" } }
```

Returns `201` with the user object.

**The `uid` must not contain identifying data.** No SSNs, names, phone numbers or
email addresses. It travels in URLs, and Validic states this as a HIPAA
constraint on you, not a style preference.

Only provision a user when they are actually ready to connect a device. Validic
asks you not to pre-provision from a member file — it bloats both databases.

## Step 2 — Learn the two identifiers

The user object carries both:

```json
{
  "uid": "your_id",             // use this in every subsequent URL
  "id":  "validic_internal",    // NEVER put this in a URL
  "marketplace": { "token": "...", "url": "https://..." },
  "status": "active"
}
```

Using `id` where `uid` belongs produces a **silent 404 with no helpful error**.
If you get an unexplained 404, check this first.

## Step 3 — Send the user to the Marketplace

Redirect them to `marketplace.url` **exactly as returned**. Never construct
the URL — it is environment-specific.

Need a fresh one? `generate-new-marketplace-token` —
`POST /organizations/{ORG_ID}/users/{uid}/marketplace?token=`

Three flavors, same URL:

| Flavor | How | You build the UI? |
|---|---|---|
| Standard | open `marketplace.url` as-is | no |
| Custom | append `&format=json` | yes (cloud + cellular) |
| Unified | request `/unified` with `&format=json` | yes (adds mobile; needs the Inform SDK and Validic Support to enable it) |

Plan a route **back** to the Marketplace — disconnecting a source happens there
too.

## Step 4 — Wait

First sync can take a couple of minutes. An empty `data: []` immediately after
connecting is normal, not a failure.

## Step 5 — Read the data

`GET /organizations/{ORG_ID}/users/{uid}/{type}?token={ORG_TOKEN}&start_date=YYYY-MM-DD&end_date=YYYY-MM-DD`

`{type}` is one of `summaries` `measurements` `workouts` `sleep` `nutrition`
`intraday` `cgm` — operationIds `summaries`, `measurements`, `workouts`,
`sleep`, `nutrition`, `intraday-2`, `cgm`.

Every one returns the same envelope:

```json
{ "data": [ { "id": "...", "type": "summary", "start_time": "...", "end_time": "...",
              "source": { "type": "fitbit" },
              "metrics": [ { "type": "steps", "value": 8432, "unit": "count", "origin": "device" } ],
              "user": { "uid": "your_id" } } ] }
```

Always unwrap `data` before iterating. Single-resource GETs return the object
**without** the envelope — the shape changes between list and item.

Shortcuts:
- Latest record only: `GET .../users/{uid}/{type}/latest?token=`
- Whole organization: `GET /organizations/{ORG_ID}/{type}?token=&start_date=&end_date=`

## Rules that will bite you

**Date window.** The API reference says max 30 days per request; Validic's agent
guide says 31. Page at **30** to be safe. Over the limit you get
`{"status":400,"error":"Bad Request"}` with nothing useful in the body. For
backfills, walk month by month.

**Rate limit.** 2,000 requests per access token per hour. No `RateLimit-*`
header, no `Retry-After` — you must count your own calls.

**Data window.** One year is retrievable over the API. Seven years are retained
for HIPAA compliance and require a support request.

**Errors.** Four different envelopes are in the wild — `{"errors":[...]}`,
`{"errors":{"field":[...]}}`, `{"status":n,"error":"..."}` and `{"message":"..."}`.
Branch on the HTTP status, not on the body shape. There are no stable error codes.

**404 is not always missing.** `GET /users/{uid}/apps?token=` returns 404 when
the user simply has no connected sources. Treat it as empty.

**`source.type` moves.** It has been renamed twice (`nokia` → `withings`,
`under_armour` → `mapmyfitness`). Do not hard-code it as an enum.

**No retries for free.** There is no idempotency key on this API. A POST that
times out cannot be safely replayed — read back with `get-all-user-profiles`
before re-provisioning.

## Don't use REST for bulk

Validic says so directly: the REST API is for user management and one-off
per-user calls. For whole-population retrieval, use the Streaming API — see
`validic-stream-real-time-health-events`.
