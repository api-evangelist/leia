---
name: Convert a 2D video to 3D or Apple Vision spatial video
description: Price the job, pre-authorize the credit spend, submit an asynchronous video conversion, and handle the completion callback — including the length-mismatch rejection.
api: openapi/leia-immersity-cloud-api-openapi.yml
operations:
  - get-access-token-1
  - ProductPricingController_getPrices
  - StorageController_getStoragePresignedUrl
  - TransactionController_generateVideoConversion
  - TransactionController_generateSpatialVideoConversion
generated: '2026-08-01'
method: generated
source: openapi/leia-immersity-cloud-api-openapi.yml, asyncapi/leia-callbacks.yml, conventions/leia-conventions.yml
---

# Convert a 2D video to 3D or spatial video

Base URL: `https://api.immersity.ai`. Authenticate with the client-credentials bearer token
(`get-access-token-1`), as in `leia-image-to-3d-animation.md`.

Video conversion is the only **asynchronous** part of the Immersity surface: the request returns
immediately and the finished job is announced on a callback URL you host.

## 1. Measure and price the job

Video requests carry a **pre-authorization**: you must send the true `videoLength` in seconds and a
`creditAmount` you calculated yourself. Both are verified server-side and a mismatch rejects the job.
Call `GET /api/v1/prices` (`ProductPricingController_getPrices`) for current per-product credit prices, and
measure the source file before submitting.

## 2. Prepare a writable output URL and a callback endpoint

- Output: `GET /api/v1/get-upload-url` (`StorageController_getStoragePresignedUrl`), or your own presigned
  PUT URL.
- Callback: a public HTTPS endpoint of yours. **`callbackUrl` is required** on both video operations.
  Immersity publishes no signature or verification scheme for the callback, so treat the payload as
  untrusted: correlate on the `correlationId` you generated, then verify the artifact by reading your own
  storage URL rather than by trusting the notification body.

## 3. Submit the conversion

`POST /api/v1/video` (`TransactionController_generateVideoConversion`)

```json
{
  "correlationId": "<fresh uuid>",
  "inputVideoUrl": "<readable source video URL>",
  "resultPresignedUrl": "<writable output URL>",
  "videoLength": 10,
  "creditAmount": 10,
  "callbackUrl": "<your https endpoint>",
  "outputWidth": 1920,
  "outputHeight": 1080
}
```

`outputType` is one of `sbs` (default), `tb`, `depth`, `depth_exr`, `spatial`, `anaglyph`. Optional:
`gain` (−10..10, default −1), `convergence` (−1..1, default 0), `dilateRatio` (0..20, default 3),
`autoConvergence`, `inputType`, `bitrate`. Note the framing rule the spec states: for stereo output
`outputWidth` is **half** the total width; for top-bottom, `outputHeight` is half the total height.

For Apple Vision output use `POST /api/v1/spatial`
(`TransactionController_generateSpatialVideoConversion`) — required fields are `inputVideoUrl`,
`resultPresignedUrl`, `videoLength`, `creditAmount`, `callbackUrl` and `inputType` (`sbs` | `tb`).

## 4. Handle the callback

When processing finishes or fails, Immersity POSTs to your `callbackUrl`. The payload schema is not
published, so key off `correlationId` and then fetch the result from your own storage URL.

Known failure code: **`ERROR_VIDEO_LENGTH_MISMATCH`** — the `videoLength` you sent did not match the actual
file. Re-measure and resubmit with a matching `creditAmount`; do not retry with the same values.

## Rules

- **401** invalid/expired token — re-mint. **400** invalid parameters. The video operations declare no 402,
  but the pre-authorized `creditAmount` must match Immersity's own calculation or the request is rejected.
- No idempotency: resubmission is a fresh, separately-billed job.
- No documented retry policy on the callback — make your endpoint idempotent on `correlationId`.
- Full callback surface: `asyncapi/leia-callbacks.yml`.
