# Leia

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

Leia Inc. is the Silicon Valley company behind **Immersity**, a platform for immersive 3D experiences on
everyday devices. Spun out of HP Labs in 2014 by physicist David Fattal, Leia pairs a Switchable-Display
hardware stack (nano-optics, a liquid-crystal switchable layer and per-pixel panel calibration, shipped in
Lume Pad, Acer SpatialLabs, Samsung Odyssey 3D, ASUS, Nubia and zSpace devices) with Spatial AI software
that converts flat 2D photos and video into stereo, top-bottom, LIF and Apple Vision spatial output.
Formerly known as LeiaPix; acquired Dimenco and the Philips 3D patent portfolio in 2023.

- Website: https://immersity.ai/
- Developers: https://immersity.ai/developers
- API docs: https://docs-api.immersity.ai/docs/getting-started
- GitHub: https://github.com/LeiaInc

## APIs

| API | Base URL | Contract |
|---|---|---|
| Immersity Cloud API | `https://api.immersity.ai` | `openapi/leia-immersity-cloud-api-openapi.yml` (OpenAPI 3.0.0, 11 operations) |
| Immersity AI Authentication API | `https://auth.immersity.ai/auth/realms/immersity` | `openapi/leia-immersity-authentication-openapi.yml` |

The Immersity Cloud API is a credit-metered media-transformation surface: disparity (depth) estimation,
3D animation generation, stereo SBS / top-bottom rendering, LIF encode/decode, 2D-to-3D and Apple Vision
spatial video conversion, and presigned Leia Storage uploads. Auth is OAuth 2.0 client credentials against
an Immersity Keycloak realm.

## Artifacts

`openapi/` · `agentic-access/` · `asyncapi/` (HTTP callbacks) · `authentication/` · `changelog/` ·
`conformance/` · `conventions/` · `data-model/` · `errors/` · `examples/` · `lifecycle/` · `llms/` ·
`mcp/` (candidate) · `overlays/` · `packages/` · `plans/` · `scopes/` · `security/` · `skills/` ·
`well-known/`

Not present, and deliberately not authored: no A2A agent card is published on any Leia host, no AsyncAPI
document exists, no security.txt / trust center / status page / deprecation policy was found, and no
first-party client library is published on any public package registry.
