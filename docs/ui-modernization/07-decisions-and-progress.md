# Decisions, open questions and progress

Last updated: 2026-10-20. Planning baseline: `cdc8450924e9338ab564c3fd76ad998e7b7cbb84`.

## Accepted requirements

| ID | Decision | Basis |
| --- | --- | --- |
| D01 | Preserve legacy UI and default root; add an alternative alongside it | User request |
| D02 | Smaller transfer, better UX and responsive layouts on ESP32-CAM | User request |
| D03 | Full legacy SD-UI feature parity before considering alternative usable | User's explicit scope answer |
| D04 | Plan/context in Markdown and multiple separate implementation prompts | User request |
| D05 | This turn is analysis/planning only | User: "Lets plan and discuss this" |
| D06 | Use Preact + TypeScript + Vite | User: "I like the stack" |
| D07 | Support modern devices/browsers; no special old-browser requirement | User answer 2 |
| D08 | A spare ESP32-CAM is available for staged hardware tests | User answer 3; connection details/results pending |
| D09 | Keep work primarily frontend; necessary small firmware changes and missing endpoints are allowed | User answer 4 |
| D10 | Improve caching with ETags and `no-cache` revalidation | Explicit caching requirement in answer 4; stage 00b |
| D11 | Start simple with an overall facelift and familiar workflows | User answers 5 and 6 |
| D12 | Defer broader page reimagining to the user's later review | User answer 6; not a first-pass deliverable |

## Proposed defaults, not yet confirmed

| ID | Proposal | Reason / revisit point |
| --- | --- | --- |
| P02 | `web-ui/` source, `/html/ui/` deployment, `/ui/index.html` + hash routes | Existing wildcard can map files without SPA fallback/new handlers; verify on device |
| P03 | Offline-capable LAN operation with bundled dependencies, no CDN | Current assets are SD-hosted; sensible device constraint, confirm unusual needs |
| P06 | SD wizard fully replaced; embedded AP provisioning and USB webinstaller preserved | Those are separate firmware/USB surfaces; revisit only if requested |
| P07 | No legacy menu edit initially; direct alternative URL | Keeps current UI untouched; optional discoverability link later |
| P08 | Conservative byte budgets, <=2 ordinary app reads and exclusive stream/write modes | Firmware sockets and blocking stream; tune with device evidence |

Former P01/P04 are resolved by D06/D07. Former P05 is superseded by D09/D10: caching firmware work is required, and focused supporting changes may be implemented as needed. The accepted direction does not authorize deployment or flashing an unspecified device.

## Open discussion points

No further product clarification is needed to finish the plan. Resolve these practical details when their stage needs them; routine implementation choices can proceed within D01-D12.

- Record the spare board/camera/SD/firmware details, device address and how sanitized fixtures/performance measurements will be collected before hardware tests.
- Select concrete modern-browser minimum versions and compatible development tool versions in stage 00.
- Should an optional "New UI" link ever be added to legacy, or should the alternative remain accessible by bookmark only?
- Determine the smallest needed API extensions from concrete gaps, then implement/document them within the relevant stage. Atomic-save guarantees need evidence, especially across power loss on FAT.
- Detailed visual/theme expansion waits for later page review; keep the initial facelift restrained and recognizable.

## Repository evidence and limits

- Working tree was clean before planning files were added; no applicable `AGENTS.md` was found in the workspace or checked parent paths.
- Read firmware handlers, SD HTML/JS, parameter generator and packaging workflow.
- Measured individual source asset raw/gzip sizes with Python; details in document 05.
- Checked official Preact/Vite documentation for build/routing proposals; links in document 04.
- Planning documents and numbered prompts are validated for local links, balanced code fences and trailing whitespace after revisions.
- Recorded all six user answers, added the cache/API-support design and dedicated stage 00b; this is a planning revision only.
- No device URL/connection or runtime capture was provided. No firmware build, browser/device test, OTA, write endpoint or deployment was performed.
- Alternative UI implementation, dependency installation and firmware changes have **not** started.

## Stage ledger

| Stage | State | Handoff / evidence |
| --- | --- | --- |
| Planning | complete | Documents 01-08 and 15 implementation prompts (00,00b,01-13); agreed direction and source analysis only |
| 00 Contracts/baseline | not started | Next implementation prompt; stack/scope/preferences settled |
| 00b Firmware cache/API support | not started | Required ETag/cache work; compatible API additions as needed |
| 01 Foundation | not started | — |
| 02 API layer | not started | — |
| 03 Dashboard | not started | — |
| 04 History/diagnostics | not started | — |
| 05 Configuration engine | not started | — |
| 06 Configuration UI | not started | — |
| 07 Camera/alignment | not started | — |
| 08 ROI editors | not started | — |
| 09 Files/backup/export | not started | — |
| 10 OTA/reboot | not started | — |
| 11 Setup/recovery | not started | — |
| 12 Packaging/coexistence | not started | — |
| 13 Qualification | not started | — |

## Handoff rules

Future sessions append specific decisions, do not quietly convert proposals into user-approved requirements, and keep untested device claims pending. Create handoff files when a stage is worked; none are pre-populated with invented results. For each handoff record scope, changed files, checks/results, size measurements, unresolved contracts, device checks and next action. Feature status belongs in document 02 as well as stage status here.

Current next step when implementation is requested: execute prompt 00, then 00b and 01-13. Stack, modern-device scope, spare hardware, focused firmware support and simple/familiar visual direction are settled; do not ask those questions again. This planning revision does not start the rewrite.
