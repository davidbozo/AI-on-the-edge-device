# Shared instructions for implementation prompts

Each numbered file is a ready-to-use prompt for one implementation conversation. Paste its fenced prompt into a new conversation with this repository open. Run them in the order in [06-work-plan.md](../06-work-plan.md). The planning turn itself does not execute them.

## Context every stage must load

Read repository instructions plus these files:

- `docs/ui-modernization/README.md`
- `docs/ui-modernization/prompts/README.md`
- `docs/ui-modernization/07-decisions-and-progress.md`
- `docs/ui-modernization/02-feature-parity.md`
- `docs/ui-modernization/08-cache-and-firmware-support.md`
- `docs/ui-modernization/09-bundling.md`
- The current stage's referenced design documents and prior relevant handoffs.

Check the actual source and working tree. If the source baseline has changed, reconcile affected contracts; do not repeat the entire investigation without cause. Preserve unrelated user edits. Follow the current conversation's authorization and any accepted changes to this plan.

## Invariants

- Keep the legacy root/default pages, workflows and existing API/integration clients intact. The accepted stack is Preact + TypeScript + Vite, targeting modern devices; preserve familiar interactions with a simple responsive facelift. Necessary small compatible firmware/API changes are allowed, and ETag/cache improvements are required. Source goes under the chosen isolated alternative directory (`web-ui/` proposed); generated output is staged separately.
- Full parity is required for release. Early screens are checkpoints; no legacy iframe/link counts as a completed replacement feature.
- No runtime CDN, Node service on-device, SSR or broad firmware/product redesign. Use existing endpoints where sufficient; implement/document/test missing endpoints when needed. Never make the frontend assume a speculative route exists.
- Small bundles alone are insufficient: bound application requests, avoid heavy eager preloads, and respect exclusive stream/write/update operation states.
- Follow the user's dependency policy: favor browser APIs and small helpers for fairly straightforward work, and treat legacy packages only as comparison baselines. Existing dependencies may be replaced. Beyond accepted Preact, justify runtime packages by actual feature fit and measured marginal gzip/transitive/plugin/request cost; do not add them merely because they fit a budget. If a dependency tradeoff is unclear, present native/custom/library options and ask the user before choosing/introducing it. Continue independent work while waiting; preserve full parity.
- Ordinary preview/page load must not mutate the real device. Read-only device inspection is distinct from intentional calibration, config/file writes, reboot and OTA. Do not deploy or execute destructive device operations merely to complete a mock/browser check; follow the current task's authorization.
- Never commit credentials or personal meter data. Use sanitized or clearly synthetic fixtures.
- Preserve unsupported/unknown configuration content; do not silently normalize away semantics.
- Complete the concrete stage and appropriate checks. Do not claim later parity/device gates passed. If hardware is unavailable, finish independent work and document the exact pending checks.
- This prompt set does not request delegation or sub-agents.

## Required stage output

Update feature statuses in document 02 and stage state in document 07. Create `docs/ui-modernization/handoffs/NN-<stage>.md` with changed files, contract/architecture decisions, commands and actual results, gzip sizes/request counts when relevant, runtime dependency decisions (including alternatives and cost), unresolved issues, hardware checks and the next prompt. Treat fixture-only checks as `mock-verified`.

Conclude with a concise account of what changed, what was checked, material limits and the next stage. Do not create commits, publish, push or deploy unless the current conversation requests it. Document concrete evidence, contracts, compatibility and resource impact for needed firmware changes and proceed within the accepted scope; do not ask again merely because they touch firmware. A spare ESP32-CAM exists, but its connection and actual test results are still pending.
