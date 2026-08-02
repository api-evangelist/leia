---
name: Render stereo pairs and package them as LIF
description: Produce side-by-side or top-bottom stereo renders from a 2D image and its disparity map, then encode them into a Leia Image Format file for immersive displays (and decode one back).
api: openapi/leia-immersity-cloud-api-openapi.yml
operations:
  - get-access-token-1
  - StorageController_getStoragePresignedUrl
  - TransactionController_estimateMonoDepth
  - TransactionController_generateStereoSbs
  - TransactionController_generateStereoTopBottom
  - TransactionController_encodeLif
  - TransactionController_decodeLif
generated: '2026-08-01'
method: generated
source: openapi/leia-immersity-cloud-api-openapi.yml, conventions/leia-conventions.yml
---

# Render stereo pairs and package them as LIF

Base URL: `https://api.immersity.ai`. Authenticate exactly as in
`leia-image-to-3d-animation.md`: client-credentials token from the Keycloak realm, sent as
`Authorization: Bearer <access_token>`.

## 1. Disparity first

Stereo rendering consumes a disparity map. Call `POST /api/v1/disparity`
(`TransactionController_estimateMonoDepth`) with `inputImageUrl` and a writable `resultPresignedUrl`.
`TransactionController_generateStereoTopBottom` **requires** `inputDisparityUrl`;
`TransactionController_generateStereoSbs` accepts it as optional.

## 2. Side-by-side

`POST /api/v1/sbs` (`TransactionController_generateStereoSbs`)

```json
{
  "correlationId": "<fresh uuid>",
  "inputImageUrl": "<source image URL>",
  "inputDisparityUrl": "<disparity map URL>",
  "resultPresignedUrl": "<writable URL>"
}
```

`outputType` selects `sbs` (default), `anaglyph` or `spatial`. `width`/`height` default to the source
dimensions. `gain`, `gainMultiplier` and `convergence` are auto-estimated when omitted.

## 3. Top-bottom

`POST /api/v1/topBottom` (`TransactionController_generateStereoTopBottom`) — same body, but both
`inputImageUrl` and `inputDisparityUrl` are required. Same `width`/`height`/`gain`/`convergence` knobs, no
`outputType`.

## 4. Encode LIF

`POST /api/v1/encode` (`TransactionController_encodeLif`) packages images plus disparity into a Leia Image
Format file. Two shapes:

- **Mono** — `inputSingleImageUrl` + `inputSingleDisparityUrl`
- **Stereo** — `inputLeftImageUrl` + `inputRightImageUrl` (+ `inputLeftDisparityUrl` /
  `inputRightDisparityUrl`, or set `inputDisparitySource`)

Always supply `resultPresignedUrl`. `gain` and `convergence` are optional.

## 5. Decode LIF

`POST /api/v1/decode` (`TransactionController_decodeLif`) with `inputImageUrl` pointing at the LIF file and
a writable `resultPresignedUrl` returns a flat 2D image.

## Rules

- All five operations are credit-metered and return **402** on an empty balance; **401** on a bad or
  expired token; **400** on invalid parameters.
- Each output needs its own writable URL — mint them with
  `GET /api/v1/get-upload-url` (`StorageController_getStoragePresignedUrl`), valid 24 hours.
- No idempotency contract: a repeat call with the same `correlationId` re-does the work and re-spends credits.
- See `data-model/leia-data-model.yml` for the artifact dependency graph.
