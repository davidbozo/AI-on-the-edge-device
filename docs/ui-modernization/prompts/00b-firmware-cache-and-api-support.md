# Stage 00b — ETags, cache policy and focused API support

```text
Execute stage 00b of docs/ui-modernization/06-work-plan.md, after stage 00 and before 01. Read prompts/README.md, shared context, handoffs/00-contracts-and-baseline.md, 03-api-contracts.md, 08-cache-and-firmware-support.md and the HTTP/static/fileserver/auth/update firmware source.

The user has accepted Preact/TypeScript/Vite, modern devices, a spare ESP32-CAM, a simple familiar facelift, and necessary small firmware/API changes. ETag support and improved caching with no-cache are required work, not merely a proposal contingent on frontend failure. Implement focused support without reopening settled choices or restructuring recognition firmware.

Implement correct quoted validators and If-None-Match conditional GET handling with bodyless 304 responses. Apply auth and setup/error/routing decisions before conditional evaluation. HTML and mutable static assets should use private, no-cache; only verified content-hashed assets may use long-lived caching. Classify dynamic/config/log/image/operation responses so they cannot inherit stale static caching. Preserve legacy UI URLs/workflows and existing public API semantics while intentionally improving cache headers on both UIs.

Choose and document a bounded validator design using final packaged content where practical. Measure compact manifest/sidecar lookup against ESP32 memory/SD/file-handle costs. Avoid loading entire assets or hashing large files on every request. Tags must identify the actual gzip/identity representation and survive package swaps correctly; missing/stale metadata, same-size rapid replacements and ordinary upload/delete/manual modifications must never produce a false 304. Define a small generation/fixture workflow now; stage 01 and packaging stage 12 will consume it. Address existing path correctness risks if required for reliable serving.

Implement only API additions justified by stage 00's actual gaps. Specify auth, wire/error/limit contracts and compatibility before frontend consumption, preserve existing clients, and account for max_uri_handlers/max_resp_headers/registration order. Defer feature-specific additions to their owning stage if not needed yet. A structured status endpoint is a candidate, not assumed to already exist; atomic save guarantees need filesystem evidence.

Run focused firmware/contract/build checks for 200/304, weak/list/wildcard validators, zero response body, gzip headers, changed/missing files, auth, setup/error states, metadata invalidation, mutable/hashed/dynamic policies and update/cache transitions. Record added RAM/SD work and unavailable tool/device checks. The spare board is available but its connection/flash details must be established before hardware tests; this prompt does not flash an unspecified board automatically.

Write handoffs/00b-firmware-cache-and-api-support.md, update API/cache contracts and ledger, and record package integration still required in 12. Next: 01-foundation.md.
```
