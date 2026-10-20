# Implementation stages

All stages are planned. Preact/TypeScript/Vite, modern-device support and a simple facelift with familiar workflows are accepted. Full parity remains the release condition. Run stages sequentially so later prompts can rely on committed or clearly recorded earlier work. No sub-agents or parallel agent execution are required by this plan.

| Stage and prompt | Depends on | Deliverable | Exit criterion |
| --- | --- | --- | --- |
| [00 Contracts and baseline](prompts/00-contracts-and-baseline.md) | Agreed direction | Action-level parity inventory, sanitized/synthetic contracts, version targets, serving/API gap list | Existing vs required contracts and spare-device validation steps recorded |
| [00b Firmware cache/API support](prompts/00b-firmware-cache-and-api-support.md) | 00 | ETags, `no-cache` HTML/mutable-static revalidation, cache policy and justified small API additions | Conditional GET/auth/gzip/update semantics verified; firmware resource impact recorded |
| [01 Foundation](prompts/01-foundation.md) | 00b | Isolated Preact app, familiar responsive shell, hash routing, fixture preview, bundle/import-graph report | No legacy/default change, short supported paths, initial byte/request counts and justified chunk layout |
| [02 API layer](prompts/02-api-layer.md) | 01 | Adapters, shared request scheduler and exclusive operation state | Error/read retry/cancel semantics tested without writes on page load |
| [03 Dashboard and recognition](prompts/03-dashboard.md) | 02 | Readings/status/image/details/manual start | F01-F04 relevant actions work with multi-sequence/error fixtures; image/request policy measured |
| [04 History and diagnostics](prompts/04-history-and-diagnostics.md) | 03 | Equivalent graph/table/log/info screens | F19,F20,F22,F28 native behavior; chart cost measured and parity retained |
| [05 Configuration engine](prompts/05-configuration-engine.md) | 02 | Lossless INI model, field inventory, patch/save/recovery services | Round-trip/unknown/per-sequence semantics and delete/upload failures covered |
| [06 Configuration UI](prompts/06-configuration-ui.md) | 05,03 | All standard/expert forms, raw editor, help, previous value and discovery | F05,F11-F18 supported without dropping unknown settings |
| [07 Camera and alignment](prompts/07-camera-and-alignment.md) | 06 | Reference/camera editor, stream session, alignment/crop tools | F06-F08 workflows native; source-pixel transforms and disconnect lifecycle verified |
| [08 Digit and analog ROIs](prompts/08-roi-editors.md) | 07 | Shared editor geometry with complete distinct digit/analog controls | F09,F10 and detailed recognition parity; edits preserve unrelated config |
| [09 Files, backup and export](prompts/09-files-backup-and-export.md) | 02,05 | File manager, individual restore, config/data ZIP downloads | F20-F25 including replacement/readonly/error/cancel paths |
| [10 OTA and reboot](prompts/10-ota-and-reboot.md) | 09 | Checksum/progress/update state machine, reconnect screen | F26,F27 native behavior with failure fixtures and hardware pending state explicit |
| [11 Setup and recovery](prompts/11-setup-and-recovery.md) | 06-10 | Full SD-card setup wizard, help/recovery/compatibility | F29,F30; complete/abort/back/skip/restart preserve correct state |
| [12 Packaging and coexistence](prompts/12-packaging-and-coexistence.md) | 11 | Build/CI integration, both-UI packages, cache validator metadata, install/reinstall/rollback docs | F31,F32 package/cache checks; legacy root/config/help/version generation preserved |
| [13 Full parity qualification](prompts/13-parity-and-device-qualification.md) | 12 | Final action matrix, browser/device/performance evidence and remaining fixes | No required parity gaps; real-device checks pass or release remains explicitly pending |

Stage 04's full data-file browser and stage 09's shared file tooling may share a small adapter. Do not implement two file managers. Stage 05 may use stage 02's transport but must remain testable without rendering the app. The table order is intentional even when some dependencies would allow independent coding.

## Stage completion and handoff

Every stage:

1. Read this plan, the progress ledger, prompt and relevant source. Recheck local repository instructions and existing work.
2. Work within the stage and fix issues necessary for its deliverable. Necessary small compatible firmware/API changes are authorized; record their contracts, tests and resource impact rather than requesting approval again solely because firmware is involved. Broad backend/product redesign remains outside scope.
3. Run appropriate build/contract/browser checks. Hardware operations are separate from a mock test; report exact evidence and pending work.
4. Update feature statuses without treating fallback links or mocks as device verification.
5. Add a stage handoff under `docs/ui-modernization/handoffs/NN-*.md`, containing changed files, contract decisions, commands/results, measured bundle sizes, limitations, device checks and the precise next step.
6. Update [07-decisions-and-progress.md](07-decisions-and-progress.md), linking that handoff. Leave the working tree reviewable. Do not create commits/PRs/pushes unless separately requested.

## Decision points

- After 00: select concrete tool/browser versions for the accepted stack/modern-device scope, finalize the parity inventory and identify firmware/API gaps. Implement the smallest required compatible support changes in 00b or their owning feature stage.
- After 00b: verify HTML/ETag/304 behavior, validator generation/invalidation, gzip and legacy compatibility. Plan spare-device smoke testing early; do not postpone all firmware validation until stage 13.
- After 01: check production output and request waterfalls over HTTP/1.1 against document 09. Benchmark one combined app including Preact/dependencies plus one CSS first, then evaluate only substantial heavy-library splits before broader feature splits. Compare vendor/app update-cache benefits separately; no per-package/per-page chunks without evidence.
- After 05: evaluate existing non-atomic config writes and add a focused replacement/concurrency endpoint if needed; prove any claimed reliability/atomicity rather than assuming a rename guarantees power-loss safety.
- After 07: verify stream/camera behavior on the spare ESP32-CAM once connected; this is a greater architectural risk than visual styling.
- After 12: inspect actual install/update artifacts before any deployment, including cache/update recovery.
- After 13: full-parity acceptance requires real device evidence. Root switching is a different future decision; successful alternative release does not replace legacy by default.

## Risks that should shape implementation

| Risk | Mitigation |
| --- | --- |
| "Small framework" but large final app | Enforce per-entry and lazy-group byte reports from stage 01 |
| Many parallel assets/API calls exhaust sockets | Few grouped chunks, measured preload behavior, shared request queue |
| UI smaller but refresh traffic larger | Measure bytes/minute and interval against legacy, include image traffic |
| Configuration loses unusual options | Lossless patches, real/synthetic edge fixtures, unknown preservation, raw editor |
| Save fails after delete | Retain original locally, verify and expose recovery; no atomicity claim |
| Stream blocks HTTP or leaves light on | Explicit exclusive mode, disconnect on stop/navigation, device validation |
| Mobile editor writes wrong coordinates | Source-pixel geometry, separate fit/zoom/DPR transforms, keyboard/numeric alternatives |
| OTA drops one UI or cached app references missing chunks | Combined packaging/validator metadata, ETag HTML revalidation, old-entry/open-tab fixtures and migration/recovery docs |
| Feature gaps discovered late | Action-level matrix in stage 00 and per-stage evidence updates |
| Spare hardware not connected/prepared | Record board/address/test setup when needed; finish offline work and leave actual device gates pending |

No time estimate is assigned yet. Configuration fidelity and calibration feature details should be verified before estimating effort.
