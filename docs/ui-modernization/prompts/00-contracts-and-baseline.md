# Stage 00 — Verify contracts, inventory and baseline

```text
Execute stage 00 of docs/ui-modernization/06-work-plan.md. Read and follow prompts/README.md and the shared context. This stage verifies the plan before implementation; do not scaffold the application yet.

Read 01-current-system.md, 03-api-contracts.md, 04-architecture.md, 05-performance-ux-validation.md and 08-cache-and-firmware-support.md, then compare their claims against current source. The user accepted Preact/TypeScript/Vite, modern devices, a spare test ESP32-CAM, necessary focused firmware/API work and a simple facelift preserving familiar workflows; do not reopen these choices.

Produce an action-level parity inventory for F01-F33 by reading actual legacy controls and firmware handlers and the agreed cache requirement. Pay special attention to graph controls, model management, camera sensor options, reference/alignment files, digit synchronization/spacing/aspect controls, analog direction/options, all INI fields, SD setup finish/abort and individual-file restore. Separate SD UI workflows, backend-only endpoints, added cache support and firmware AP/USB surfaces. Do not mark anything implemented.

Record exact wire contracts and a sanitized/synthetic fixture catalogue: /json, /sysinfo, recognition HTML, tab-separated lists, directory HTML, CSV, tail logs, config grammar, upload/delete/MD5 responses, prevalue errors and OTA scheduling. Map every camera/calibration query and filename limit. If actual responses are unavailable, identify source-derived fixtures explicitly and keep runtime checks pending.

Reproduce source raw/gzip baseline; if generating release HTML, do it in isolated temporary/staging input so the legacy tooltip generator cannot modify working source. Record tooling versions and measurements. Select supported tool versions and concrete modern-browser minimums for the accepted stack; do not add legacy-browser support or broader visual redesign.

Document a serving compatibility probe for nested short-path .js/.css/.html gzip assets, auth, static path handling, cached entry behavior and blocking stream disconnect. The spare device is available but address/setup details are pending; run read-only probes only against an identified authorized target. Record small firmware/API gaps with concrete contracts for stage 00b or the owning feature stage. ETag/no-cache work is required even if the current frontend appears functional.

Finish with an updated plan, action inventory/fixture catalogue under docs/ui-modernization, and handoffs/00-contracts-and-baseline.md. State the next prompt: 00b-firmware-cache-and-api-support.md.
```
