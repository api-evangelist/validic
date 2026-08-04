# Validic (validic)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Validic is an enterprise health-data platform that connects patient-recorded data from digital health applications, medical devices, and wearables to healthcare organizations. Its Inform API and Mobile SDK provision users against an organization, present a hosted Marketplace for connecting API/cloud and Bluetooth sources, and return standardized health observations - CGM, intraday activity, point-in-time measurements, nutrition, sleep, daily summaries, and workouts. Validic also manages cellular-enabled devices, exposes connection-event history, and delivers data in real time through a Server-Sent Events (SSE) Streaming API and a webhook Push Service. The platform is HITRUST-certified and HIPAA-compliant. Requests authenticate with an organization access token passed as the `token` query parameter over HTTPS.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/validic/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/validic/refs/heads/main/apis.yml)

## Tags

- Health Data
- Digital Health
- Wearables
- Remote Patient Monitoring
- Health IoT
- Interoperability
- HIPAA

## Timestamps

- **Created:** 2026-07-05
- **Modified:** 2026-07-05

## APIs

### Validic Users API

Provision, list, retrieve, and delete users under an organization. A newly provisioned user returns Marketplace and mobile connection tokens plus location metadata; users should only be provisioned when ready to connect a device.

- **Human URL:** [https://developer.validic.com/docs/provision-and-managing-users](https://developer.validic.com/docs/provision-and-managing-users)
- **Base URL:** `https://api.v2.validic.com`

#### Tags

- Users
- Provisioning
- Organizations

#### Properties

- [Documentation](https://developer.validic.com/docs/provision-and-managing-users)
- [API Reference](https://developer.validic.com/docs/inform-rest-api)
- [OpenAPI](openapi/validic-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/validic.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/validic.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Validic Marketplace & Connections API

The hosted Standard, Custom, and Unified Marketplace lets users connect API/cloud and Bluetooth sources; the connection-events resource returns the history of source connect (create) and disconnect (delete) events for the organization.

- **Human URL:** [https://developer.validic.com/docs/unified-marketplace](https://developer.validic.com/docs/unified-marketplace)
- **Base URL:** `https://api.v2.validic.com`

#### Tags

- Marketplace
- Connections
- Sources

#### Properties

- [Documentation](https://developer.validic.com/docs/requesting-access-to-data)
- [API Reference](https://developer.validic.com/reference/get-connection-events-by-orgid)
- [OpenAPI](openapi/validic-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/validic.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/validic.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Validic Observations & Data API

Retrieve standardized health observations for a user - continuous glucose monitoring (CGM), intraday activity time series, point-in-time measurements (weight, blood pressure, glucose), nutrition, sleep, daily activity summaries, and workouts - filtered by date range and source.

- **Human URL:** [https://developer.validic.com/docs/accessing-data](https://developer.validic.com/docs/accessing-data)
- **Base URL:** `https://api.v2.validic.com`

#### Tags

- Observations
- CGM
- Measurements
- Activity

#### Properties

- [Documentation](https://developer.validic.com/docs/accessing-data)
- [API Reference](https://developer.validic.com/docs/inform-rest-api)
- [OpenAPI](openapi/validic-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/validic.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/validic.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Validic Devices API

Activate and suspend the cellular data plan for cellular-enabled health devices so they can transmit readings without a paired phone - the backbone of Validic's remote patient monitoring device programs.

- **Human URL:** [https://help.validic.com/space/VCS/4314071049/Cellular-Enabled+Device+Support](https://help.validic.com/space/VCS/4314071049/Cellular-Enabled+Device+Support)
- **Base URL:** `https://api.v2.validic.com`

#### Tags

- Devices
- Cellular
- RPM

#### Properties

- [Documentation](https://help.validic.com/space/VCS/4314071049/Cellular-Enabled+Device+Support)
- [API Reference](https://developer.validic.com/docs/inform-rest-api)
- [OpenAPI](openapi/validic-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/validic.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/validic.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Validic Streaming API

Create a stream and connect to it over a long-lived HTTP connection that delivers organization-wide health and marketplace-connection events as Server-Sent Events (`text/event-stream`), with 5-second heartbeats, replay, and up to 5 streams per customer. This is SSE over HTTPS, not a WebSocket.

- **Human URL:** [https://developer.validic.com/docs/connect-to-a-stream](https://developer.validic.com/docs/connect-to-a-stream)
- **Base URL:** `https://streams.v2.validic.com`

#### Tags

- Streaming
- Server-Sent Events
- Real Time

#### Properties

- [Documentation](https://developer.validic.com/docs/creating-a-stream)
- [API Reference](https://developer.validic.com/docs/connect-to-a-stream)
- [OpenAPI](openapi/validic-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/validic.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/validic.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Validic Push Service API

Event-based Push Service that POSTs notifications to a customer webhook endpoint as new health data arrives - an alternative delivery model to the Streaming API for organizations that prefer to receive rather than hold a connection.

- **Human URL:** [https://help.validic.com/space/VCS/3374284801/Streaming+API+vs+Push+Service](https://help.validic.com/space/VCS/3374284801/Streaming+API+vs+Push+Service)
- **Base URL:** `https://api.v2.validic.com`

#### Tags

- Push
- Webhooks
- Notifications

#### Properties

- [Documentation](https://help.validic.com/space/VCS/3374284801/Streaming+API+vs+Push+Service)
- [OpenAPI](openapi/validic-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/validic.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/validic.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [GitHub Organization](https://github.com/validic)
- [LinkedIn](https://www.linkedin.com/company/validic)
- [Website](https://validic.com)
- [Documentation](https://developer.validic.com)
- [Plans](plans/validic-plans-pricing.yml)
- [Rate Limits](rate-limits/validic-rate-limits.yml)
- [Fin Ops](finops/validic-finops.yml)
- [Blog](https://validic.com/blog/)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
