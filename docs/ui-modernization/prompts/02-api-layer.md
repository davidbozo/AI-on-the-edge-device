# Stage 02 — Transport, adapters and operation control

```text
Execute stage 02 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, handoffs 00/01, 03-api-contracts.md and 04-architecture.md.

Implement framework-independent transport/adapters for existing root-relative device endpoints: readings, sysinfo/status/metadata, tab-separated lists, files/directory HTML, text tails and operation responses. Use stage 00 fixtures. Normalize missing/empty/malformed data without turning empty readings into zero. Preserve decimal strings, timestamps and application error bodies. Recognize initialization 403 separately from authentication 401.

Implement one application request scheduler: <=2 ordinary reads initially, no overlapping polls, completion-based scheduling, abort obsolete work on route/visibility changes, bounded backoff for safe reads only. Add exclusive operation modes for stream/camera mutation, file/config writes and OTA/reboot. Mutations, including mutation-by-GET, must never receive automatic blind retries. Canceled requests do not imply rollback.

Provide fixture transport and documented same-origin development proxy configuration. Opening the preview must not write to the device. Use implemented/documented APIs, including approved support from stage 00b. A needed missing route can be added as a focused compatible firmware extension with contracts/tests and version handling; do not assume a route exists or stop merely because firmware is involved. Directory HTML must be parsed inertly and validated, not inserted/executed as UI. Respect document 08's static vs live-state cache policies.

Add focused tests for scheduling/concurrency/cancellation, auth/initializing/malformed states, operation body failures with HTTP 200, and no repeated mutations. Verify production size impact. Do not implement all screens here.

Write handoffs/02-api-layer.md and update progress. Next: 03-dashboard.md.
```
