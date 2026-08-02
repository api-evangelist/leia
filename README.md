# Leia

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
