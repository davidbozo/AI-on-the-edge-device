# Decisions, open questions and progress

Last updated: 2026-10-21. Planning baseline: `cdc8450924e9338ab564c3fd76ad998e7b7cbb84`.

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
| D13 | Treat firmware HTTP/1.1 as an explicit bundling/performance constraint | User clarification; measure request count/waterfalls alongside transferred bytes |
| D14 | Minimize dependencies, especially shipped bundle cost; implement fairly straightforward behavior directly; choose better-fitting replacements freely | User dependency instruction; legacy dependencies are not requirements |
| D15 | Ask the user when a dependency choice is uncertain | Present actual feature needs, native/custom/library options and measured production cost before deciding |
| D16 | Provide bundle-size CLI analysis early, with the foundation build | User request; measure sizes/contributors and changes before adding feature dependencies |
| D17 | Implement iteratively prompt by prompt; treat the plan as a living guide and revise it after findings/discussion | User instruction: check legacy code when needed and ask when behavior/intent does not make sense; no rigid adherence to obsolete details |

## Proposed defaults, not yet confirmed

| ID | Proposal | Reason / revisit point |
| --- | --- | --- |
| P02 | `web-ui/` source, `/html/ui/` deployment, `/ui/index.html` + hash routes | Existing wildcard can map files without SPA fallback/new handlers; verify on device |
| P03 | Offline-capable LAN operation with bundled dependencies, no CDN | Current assets are SD-hosted; sensible device constraint, confirm unusual needs |
| P06 | SD wizard fully replaced; embedded AP provisioning and USB webinstaller preserved | Those are separate firmware/USB surfaces; revisit only if requested |
| P07 | No legacy menu edit initially; direct alternative URL | Keeps current UI untouched; optional discoverability link later |
| P08 | Conservative byte budgets, <=2 ordinary app reads and exclusive stream/write modes | Firmware sockets and blocking stream; tune with device evidence |
| P09 | Benchmark one combined app including Preact/dependencies plus one CSS first; split only measured substantial specialist code | Revised for HTTP/1.1: prefer three initial static requests, zero code requests for ordinary navigation; compare heavy-library splits/vendor-app cache tradeoffs in document 09 |
| P10 | Built-in Vite gzip reporting + small Node size/budget CLI + dev-only rollup-plugin-visualizer | Document 05: repeatable exact-output metrics and module attribution, no added runtime code; verify locked-version compatibility in 01, wire CI in 12 |

Former P01/P04 are resolved by D06/D07. Former P05 is superseded by D09/D10: caching firmware work is required, and focused supporting changes may be implemented as needed. The accepted direction does not authorize deployment or flashing an unspecified device.

## Open discussion points

No blocking product clarification is currently needed for this planning revision. New questions may arise during implementation; inspect relevant legacy code and ask when behavior, intent or a meaningful tradeoff remains unclear. Routine implementation choices can proceed within D01-D17. Unclear dependency tradeoffs must be brought to the user before selecting/introducing the package; this is not a blanket requirement to reconfirm the already accepted stack.

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
- Added bundling analysis after the user's dependency/app split question; request-count goals and implementation comparisons are documented, with the exact chunk layout still proposed.
- Revised the bundle benchmark order after the user's HTTP/1.1 clarification: a combined application first, optional heavy-library splits next, broader feature splits only with evidence.
- Added document 09's complex-feature dependency inventory and repeated existing-library gzip measurements on 2026-10-21: charting/ZIP/checksum are the strongest candidates; calibration/configuration/workflows primarily need application-specific code and browser APIs. No replacement packages are selected or installed.
- Recorded the user's dependency policy and revised library-leaning recommendations: compare native/focused custom implementations first where straightforward, evaluate better-fitting replacements, and ask with evidence if uncertain. The feature inventory does not preapprove chart, ZIP or MD5 packages.
- Added the user's early CLI analysis requirement to stage 01, proposing built-in Vite gzip output, a small Node reporter/checker and development-only module visualization. Commands are planned; no analyzer or frontend toolchain has been installed/run.
- Recorded iterative implementation as a working requirement: stage order/details may adapt, unclear behavior should be checked against source/discussed, and affected context/prompts/handoffs must be updated after changes rather than forcing outdated instructions.
- No device URL/connection or runtime capture was provided. No firmware build, browser/device test, OTA, write endpoint or deployment was performed.
- Alternative UI implementation, dependency installation and firmware changes have **not** started.

## Stage ledger

| Stage | State | Handoff / evidence |
| --- | --- | --- |
| Planning | complete | Documents 01-09 and 15 implementation prompts (00,00b,01-13); agreed direction, proposed bundling and source analysis only |
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
