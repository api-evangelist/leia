---
name: Turn a 2D image into a 3D animation with Immersity
description: Authenticate against the Immersity Keycloak realm, mint a writable storage URL, estimate a disparity map from a flat image, then render a looping 3D parallax animation.
api: openapi/leia-immersity-cloud-api-openapi.yml
operations:
  - get-access-token-1
  - StorageController_getStoragePresignedUrl
  - TransactionController_estimateMonoDepth
  - TransactionController_generateAnimation
generated: '2026-08-01'
method: generated
source: openapi/leia-immersity-cloud-api-openapi.yml, conventions/leia-conventions.yml
---

# Turn a 2D image into a 3D animation

Base URL: `https://api.immersity.ai`

## 1. Get an access token

`POST https://auth.immersity.ai/auth/realms/immersity/protocol/openid-connect/token`
(operation `get-access-token-1`) with form fields `client_id`, `client_secret`,
`grant_type=client_credentials`. Use the returned `access_token` as
`Authorization: Bearer <access_token>` on every call below. Client ID/secret pairs are created in the
Immersity account console (Leia Account → Manage Account → API tab). Never embed them in client code.

## 2. Get writable result URLs

Every Immersity operation writes its output to a caller-supplied URL that must carry HTTP PUT permission.
Either bring your own S3 / GCS / Azure presigned URL, or call
`GET /api/v1/get-upload-url` (`StorageController_getStoragePresignedUrl`) once per output artifact. It
returns `{url, urlExpiresUTC}` — a Leia Storage URL valid for 24 hours. You need two: one for the disparity
map, one for the animation.

## 3. Estimate the disparity map

`POST /api/v1/disparity` (`TransactionController_estimateMonoDepth`)

```json
{
  "correlationId": "<fresh uuid>",
  "inputImageUrl": "<publicly readable source image URL>",
  "resultPresignedUrl": "<writable URL #1>"
}
```

Optional: `dilation` (default 0.0063), `inputType` (`image2d` | `image360`),
`outputBitDepth` (`uint8` | `uint16`). A 200 echoes `{correlationId, resultPresignedUrl}`; the disparity map
itself is written to URL #1. Allow up to 3–5 minutes of client timeout.

## 4. Render the animation

`POST /api/v1/animation` (`TransactionController_generateAnimation`)

```json
{
  "correlationId": "<fresh uuid>",
  "inputImageUrl": "<same source image URL>",
  "inputDisparityUrl": "<readable URL #1>",
  "resultPresignedUrl": "<writable URL #2>"
}
```

Both `inputImageUrl` and `inputDisparityUrl` are required. Tune with `animationType` (`mp4` | `gif`),
`animationLength` (default 4s), `outputWidth`/`outputHeight`, `amplitudeX/Y/Z`, `phaseX/Y/Z`,
`numberOfLoops`, `outputFps`. Leave `gain` and `convergence` unset to let Immersity estimate them from the
image. For a bespoke camera path, pass `pattern` as a comma-separated `{x1,y1,z1,x2,y2,z2,…}` list — one
triplet per frame; `animationLength`, `amplitude*` and `phase*` are then ignored.

## Rules

- **Credits, not rate limits.** Each call is metered in credits (documented flat rate: 10 per call; 100 free
  credits on a new account, non-commercial use only). Price the work first with
  `GET /api/v1/prices` (`ProductPricingController_getPrices`). HTTP **402** means the balance is empty —
  do not retry, top up.
- **No idempotency.** `correlationId` correlates a request; it does **not** deduplicate. Generate a fresh
  UUID per call and never blind-retry a call that may have succeeded — a retry spends credits again.
- **Errors.** 400 = invalid parameters (check required fields and the numeric bounds: `gain` −10..10,
  `convergence` −1..1, output dimensions ≤ 8000). 401 = expired or invalid token; re-mint it. No error
  response carries a documented body schema — branch on the status code. See `errors/leia-problem-types.yml`.
- **Read the result from your own storage**, never from the response body.
