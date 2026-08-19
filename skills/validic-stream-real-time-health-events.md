---
name: validic-stream-real-time-health-events
description: >-
  Create a Validic stream and consume organization-wide health events in real
  time over Server-Sent Events, including replaying history and surviving the
  reconnect hazard. This is the correct path for whole-population data, not the
  REST API.
api: Validic Streaming API
generated: '2026-08-15'
method: searched
source: >-
  https://developer.validic.com/docs/creating-a-stream +
  https://developer.validic.com/docs/connect-to-a-stream +
  https://dashboard.validic.com/inform-quickstart.txt +
  openapi/_original/validic-streaming-resources-openapi.json
operations:
  - create-a-stream
  - get-a-stream
  - get-a-stream-by-id-1
  - update-a-stream-1
  - delete-a-stream-1
---

# Stream real-time health events

**Base:** `https://streams.v2.validic.com` — a *different host* from the REST API.
**Auth:** `?token=ORG_TOKEN`, same organization token.

A stream is organization-wide: it carries events for every user under the org,
not per user.

## Step 1 — Create the stream once, reuse the id

`create-a-stream` — `POST /streams?token={ORG_TOKEN}`

```json
{ "name": "meaningful-customer-name", "start_date": "2026-08-01",
  "resource_filter": ["summary", "measurement"] }
```

Returns `{ "id": "stream_id", "name": "...", "created_at": "..." }`.

**Set filters at creation.** They are properties of the stream, not of a
connection — that is deliberate, so every client attached to the stream sees a
consistent view. Changing what you receive means updating (`update-a-stream-1`)
or recreating the stream.

- `resource_filter`: `measurement` `cgm` `workout` `summary` `sleep` `intraday` `nutrition`
- `event_type_filter`: `data` `rule` `connection`

You get **5 streams per customer**. Creating a sixth errors. List what you
already have with `get-a-stream` (`GET /streams?token=`) before creating another.

## Step 2 — Connect

`GET /streams/{stream_id}/connect?token={ORG_TOKEN}`

A long-lived HTTP connection, `Content-Type: text/event-stream`. Keep it open.

Four event types arrive:

```
event: poke        -> heartbeat every 5 seconds. Discard it.
event: data        -> a health record, identical schema to the REST response
event: rule        -> { "rule_id": "...", "user_id": "...", "triggered_at": "..." }
event: connection  -> { "user_id": "...", "source": "fitbit", "event": "connected"|"disconnected" }
```

`poke` exists to prove the stream is alive. If pokes stop, you have lost the
connection even if the socket looks open — use them as your liveness check.

## Step 3 — Handle redelivery, because you will get it

Validic delivers **at least once**. Reconnecting after a disconnect is the
common case: Validic would rather send you a record twice than lose it.

**De-duplicate on the record `id`** (`checksum` is also available). Validic puts
this on you explicitly — "it is up to the customer to ensure idempotent handling
of redelivered messages."

## Step 4 — Reconnect carefully

Up to **3 concurrent client connections per stream**. Events are distributed
across them; there is no load balancing *between* streams.

When a client disconnects, Validic starts an internal rebalance and does not
release the connection slot until it finishes. **A quick disconnect-reconnect at
max connections returns HTTP 422.** This is the one 422 on the platform that is
genuinely retryable — back off exponentially and try several times rather than
treating it as fatal.

## Step 5 — Replay instead of waiting

`GET /replay?token={ORG_TOKEN}&resources=summary,workout&date=YYYY-MM-DD`

Same SSE frame format. `resources` is comma-separated; `date` is the start point.

There is **no `Last-Event-ID` resumption**. Replay is keyed by date, not by
event id, so you can replay a day but you cannot resume exactly where you
stopped. Checkpoint by record `id` on your side and drop what you have already
seen.

## Step 6 — Tear down

`delete-a-stream-1` — `DELETE /streams/{id}?token=`

**Requires 0 active connections.** Close every client first or the delete fails.

## Webhooks are the other option

If holding an outbound connection is awkward, the Push Service delivers the same
records to an endpoint you expose, with FHIR R4 mapping available. Delivery
status per batch:
`GET /organizations/{organization_id}/notifications?token={token}` on
`https://api.prod.validic.com`. Note that host is documented nowhere else.

Push Service endpoints are configured with Validic, not self-served, and no
webhook signature scheme is published — verify authenticity by re-reading the
record over REST if provenance matters to you.
