# Stage 01 — Isolated app foundation

```text
Execute stage 01 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, handoffs 00/00b and 04-architecture.md/05-performance-ux-validation.md. Preact + TypeScript + Vite is accepted; support modern browsers with the recorded version targets.

Create an isolated frontend source project (web-ui/ unless superseded). Add a production build, lockfile, documented development commands and fixture preview. Keep dependencies small and locally bundled. Do not rewrite legacy files or change firmware/CI/default routing in this stage.

Build a simple responsive, accessible navigation shell with familiar legacy page purposes, labels/actions and workflow order, hash routes, back/forward handling, a visible legacy-root recovery link and explicit unimplemented-screen labels. Use native Preact, system fonts and small local CSS. Avoid broader navigation/page reimagining, elaborate visual effects or a mandatory theme system; the user will review redesigns later. Namespace preferences; do not use the legacy page cookie. Keep static entry/recovery text useful if JS fails.

Generate relative assets for /ui/index.html, short hashed .js/.css filenames, no unsupported MIME-dependent files or runtime external URLs. Emit a raw/gzip/initial-request report and path checks. Keep entry request fan-out small; inspect generated preload behavior. Do not gzip the source tree or copy output over sd-card/html.

Apply document 09's HTTP/1.1 bundling proposal: begin with one combined application JS including Preact/dependencies and one compact CSS. Target three initial static requests, at most four with a documented reason, and zero code requests for ordinary screen navigation. Add lazy boundaries only for substantial measured code, initially evaluating chart/archive libraries before broader settings/calibration splits. Vendor/app separation is a cache-update benchmark option, not a request-count optimization by itself. Avoid preserveModules and per-package/page/CSS fragments. Evaluate cssCodeSplit:false and inspect preload/connection/waterfall behavior before disabling it. Keep full-app comparisons pending until representative code exists; report any request-vs-byte-budget tradeoff instead of benchmarking stubs as final results.

Consume the validator format/cache contract defined in stage 00b; generate tags from final output bytes through the agreed build step. Keep package integration for stage 12 and recovery from pre-upgrade cached HTML documented.

Check production build/type correctness and render shell/navigation at narrow/wide viewports using available browser tooling. Measure initial gzip size against document 05 and explain deviations. Functional screens remain planned.

Write handoffs/01-foundation.md and update the ledger/parity evidence. Next: 02-api-layer.md.
```
