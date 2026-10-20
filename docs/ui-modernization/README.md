# Alternative SD-card UI plan

Planning date: 2026-10-20. Source baseline: `cdc8450924e9338ab564c3fd76ad998e7b7cbb84`.
Status: repository analysis and agreed direction; implementation has not started. Updated with user preferences on 2026-10-20.

## Agreed requirements

- Add a lightweight, responsive alternative alongside the current UI.
- Preserve the legacy UI, its URLs, and its default entry point.
- Reduce bytes transferred and improve everyday use on phones and desktops.
- **Full feature parity is required before the alternative is considered usable.** The user selected this scope explicitly. Intermediate stages are development checkpoints, not reduced-scope releases.
- Document the context and divide implementation into separate prompts that can run in fresh conversations.
- Use Preact + TypeScript + Vite and target modern devices/browsers.
- Start with a simple visual facelift and responsive layout while preserving familiar workflows. Broader page redesign follows the user's later review.
- Keep the project primarily frontend, with focused firmware changes or added endpoints where needed. ETag support and improved cache policy, including `no-cache` revalidation, are required work.
- A spare ESP32-CAM is available for validation; connection details and actual test results are still pending.
- This change adds planning documents only. It does not install dependencies, change firmware, deploy files, or rewrite UI code.

## Read these documents

| Document | Purpose |
| --- | --- |
| [01-current-system.md](01-current-system.md) | SD-card hosting, build pipeline, firmware limits, source map |
| [02-feature-parity.md](02-feature-parity.md) | Feature inventory and release checklist |
| [03-api-contracts.md](03-api-contracts.md) | Existing endpoints, payloads, mutation semantics, unknowns |
| [04-architecture.md](04-architecture.md) | Accepted stack and proposed coexistence, routing, data flow, deployment |
| [05-performance-ux-validation.md](05-performance-ux-validation.md) | Measured asset baseline, proposed budgets, UX and device checks |
| [06-work-plan.md](06-work-plan.md) | Ordered implementation stages and exit criteria |
| [07-decisions-and-progress.md](07-decisions-and-progress.md) | Accepted requirements, proposals, open questions, handoff state |
| [08-cache-and-firmware-support.md](08-cache-and-firmware-support.md) | ETags, cache policy, conditional requests and focused API extensions |
| [09-bundling.md](09-bundling.md) | Initial request budget, coarse feature bundles, vendor split tradeoffs and measurements |

## Agreed direction and proposed deployment

Use Preact with TypeScript and Vite, producing static assets for `/sdcard/html/ui/`.
Open the alternative at `/ui/index.html`; use hash routes such as `/ui/index.html#/history`.
Keep `/` and `/index.html` serving the legacy experience, including its existing setup/error handling.
The ESP32 serves files and its existing APIs; the user's browser runs the application.

The user accepted Preact + TypeScript + Vite. The first stage verifies contracts, selects compatible tool versions and records browser targets; it does not reopen the framework choice. Deployment paths and chunk boundaries remain implementation details to verify.

Main opportunities: remove jQuery from the new UI, replace the large chart dependency with a measured alternative, load specialist tools only when opened, reuse `/json`, and avoid unnecessary image/configuration downloads. Preserve all legacy workflows through native alternative screens; legacy links are useful during development and recovery but do not count as parity.

For the HTTP/1.1 firmware, benchmark one combined application JS file including Preact/dependencies plus one compact stylesheet first: three initial static requests. Keep ordinary navigation bundled; consider extra chunks only for substantial chart/archive or other measured heavy code. Compare byte savings, request/waterfall cost and vendor/app update-cache reuse before finalizing. See document 09; the exact chunk layout remains a proposal.

Improve firmware caching early through the dedicated stage 00b: HTML and mutable static files revalidate with ETags; hashed assets can use long-lived caching. Preserve the legacy interface/default URLs while testing intentional cache-header changes on both UIs. Necessary small API additions are within scope and must preserve existing clients; the frontend must not assume an endpoint exists before it is implemented.

## How to use the prompts

1. Review the design and record any changed preferences in [07-decisions-and-progress.md](07-decisions-and-progress.md).
2. Start with [prompts/00-contracts-and-baseline.md](prompts/00-contracts-and-baseline.md).
3. Run one numbered prompt per implementation conversation in the order specified in the work plan.
4. Each prompt reads [prompts/README.md](prompts/README.md), the shared context, and the preceding handoff. Each updates the progress document and its feature evidence before finishing.
5. Keep hardware checks marked pending until performed on a real device. Browser mocks cannot establish firmware compatibility or ESP32 stability.

The prompts specify work for future turns; their presence does not start implementation now.
