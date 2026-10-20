# Architecture and implementation design

Status: Preact + TypeScript + Vite is accepted; detailed implementation choices below remain subject to evidence. The user wants a simple facelift with familiar workflows and modern-device support. Accepted requirements live in [07-decisions-and-progress.md](07-decisions-and-progress.md).

## Stack decision

| Option | Benefits for this project | Cost / tradeoff | Recommendation |
| --- | --- | --- | --- |
| Preact + TypeScript + Vite | Reusable forms/calibration components, small client runtime, typed adapters, production minification and route splitting | Development build/toolchain; size still depends on dependencies and app code | Accepted by the user |
| TypeScript + DOM APIs | No framework runtime; direct control | More manual state/lifecycle/form synchronization across many specialist screens | Good if absolute minimum bytes outweigh maintenance cost |
| Web Components + minimal helpers | Native encapsulation and component reuse | Form/state/navigation coordination still needs design; no automatic size advantage | Viable, but no clear benefit here over Preact |

Validate the accepted stack with a measured production shell rather than relying on framework marketing numbers. The alternative options above are retained as analysis, not an unresolved choice. Build tools run on the development/CI machine; TypeScript types are removed from shipped code. Neither Node nor Preact executes on the ESP32. Target current modern desktop/mobile browsers; select concrete minimum versions during stage 00 without adding a legacy-browser compatibility project.

Use native Preact APIs and JSX. Avoid `preact/compat`, React-oriented component suites, jQuery, a general state/query framework, external fonts and runtime CDNs. The user explicitly prioritizes bundle size and minimal dependencies: implement fairly straightforward behavior with browser APIs and small application helpers. Existing chart/archive/digest libraries are baselines, not required packages. Consider a better-fitting replacement or focused custom implementation against the actual parity requirements; do not carry a general library merely because legacy already uses it.

For each runtime addition beyond accepted Preact, document the required behavior, why native/simple code is insufficient, alternatives, marginal production gzip bytes including plugins/transitive code, request cost and maintenance/correctness implications. Staying under a budget does not by itself justify adding a package. If the choice is unclear, ask the user with that evidence before selecting/introducing it and continue unrelated work. Do not silently drop required interactions or data fidelity to make a custom implementation appear simpler. Build/test tools remain separate from shipped runtime dependencies and should also stay proportionate.

Preact documents a Vite build workflow in its [getting-started guide](https://preactjs.com/guide/v10/getting-started/). Vite documents [nested/relative asset paths and static production builds](https://vite.dev/guide/build) and [module-preload configuration](https://vite.dev/config/build-options.html). These links were checked during planning on 2026-10-20; select and lock mutually supported package/runtime versions at implementation time. The linked Preact v10 guide is a workflow reference, not a version pin.

## Repository and SD layout

Proposed source layout (directories below are not created by this planning change):

```text
web-ui/
  package.json / lockfile / tsconfig.json / vite.config.ts
  src/
    app/                 # shell, hash navigation, shared small UI primitives
    api/                 # wire contracts, scheduler, operation state, adapters
    config/              # lossless INI model, parameter metadata, patches
    features/            # dashboard, history, diagnostics, configuration,
                         # camera, alignment, ROIs, files, maintenance, setup
    styles/
  fixtures/              # synthetic/sanitized device responses
  scripts/               # packaging, path/MIME/size reports, fixture serving
  dist/                  # ignored static output, never a source directory
docs/ui-modernization/   # persistent implementation context
```

Packaged SD tree:

```text
/sdcard/html/
  index.html.gz          # current legacy entry, unchanged behavior
  ...                    # all other legacy/generated pages and assets
  ui/
    index.html.gz        # alternative entry
    a/
      <short-hash>.js.gz
      <short-hash>.css.gz
      ...                # a few grouped lazy chunks; supported file types
```

Build first into `web-ui/dist`; copy output into the release staging `html/ui` during packaging. Avoid committing generated assets into legacy `sd-card/html`. Stage installation scripts may produce a separate additive directory for manual SD copying, clearly distinguished from whole-HTML OTA packages.

## Coexistence and routing

- Canonical URL: `/ui/index.html`; route examples: `#/dashboard`, `#/history`, `#/configuration`, `#/camera`, `#/files`, `#/setup`.
- Use `base: './'` or an equivalent tested build setting for relative asset paths. Use root-relative device APIs (e.g. `/json`, not `json`).
- Do not rely on nested directory indexes, History API fallback, custom server routes or `/api/` aliases that do not exist.
- Keep emitted module files `.js`; avoid `.mjs`/external SVG/fonts requiring new MIME handlers.
- Default conservative path target: at most 32 ASCII characters before query/fragment for emitted `/ui/...` asset URLs. This is a design guardrail, not a proven firmware limit; stage 00 and device tests establish the actual constraint.
- Do not modify `/`, the legacy menu or the root `page` cookie initially. Use namespaced preferences such as `ui-next.*` if persistent settings are useful.
- Always provide a direct legacy-root link outside lazy modules. An optional legacy-menu link is a later explicit decision; bookmarks/direct URLs are sufficient to coexist.
- New screens implement their workflows natively. Recovery links do not count toward full parity.

## Loading and state

The firmware uses HTTP/1.1: start with one combined application JS bundle including Preact/dependencies and one compact external stylesheet, targeting three initial static requests including HTML. Merge small screens/helpers so ordinary navigation needs no further code requests. Compare one complete app against splitting only substantial specialist code, especially chart/archive libraries. Broader configuration/calibration splits need measured benefits; functional areas are not automatic output chunks. Justify shared chunks or extra CSS requests against request/waterfall cost.

See [09-bundling.md](09-bundling.md) for HTTP/1.1 connection/serialization considerations and benchmark variants. A vendor/app split can improve reuse across updates but adds an initial asset request; a blanket dependency chunk can pull chart/archive code into initial load. Keep the runtime and app together initially. Exact grouping and any initial-byte-budget tradeoff remain subject to measurement, not accepted fixed requirements.

Store readings, device metadata and configuration at the appropriate application lifetime. Load config on entering a workflow that needs it, not merely to render navigation. Fetch `/sysinfo` once for common metadata. Use `/json` for readings, plus `/statusflow` and occasional round/health information; do not pretend `/json` includes every legacy field.

The scheduler controls **application requests**: initially two ordinary reads maximum, one mutation workflow at a time, visible-screen subscriptions only, no overlap, cancellation and read-only retry policy. Disable unnecessary preload/prefetch fan-out and load screen dependencies before starting a blocking operation. A five-socket server is shared with legacy tabs, other browsers and integrations, so two is a conservative policy, not a concurrency guarantee.

Display decimal readings from original strings. Keep a previous good snapshot visible with an explicit stale marker when refresh fails. All device/config/log strings are text by default, never unsanitized HTML. No persistent credential/config-secret cache; config edits live in memory and users can deliberately download them.

## Responsive UI structure

The first pass is a simple facelift: retain familiar page purposes, labels, available actions and workflow order. Responsive navigation can use an accessible compact menu on narrow screens and a sidebar on wider screens; avoid major regrouping or reimagining page interactions before the user's later page-by-page review. The main content uses one scroll context without iframes. Use a restrained, readable appearance; a theme system or dark mode is optional later work, not a first-release requirement.

Reusable primitives should cover labelled fields, section disclosure, validation, pending operations, status/error text, progress, confirmation of destructive actions and notifications. Use system fonts and CSS Grid/Flexbox; no full visual framework is necessary. Configuration uses searchable sections and an explicit expert toggle rather than a single very wide table.

Calibration uses canvas with coordinates stored in **source-image pixels**. Convert pointer coordinates through the actual displayed image rectangle; keep device-pixel ratio and view zoom separate. Provide numeric fields and keyboard nudging as alternatives to touch dragging. Reflow controls below the image on phones. Save small focused config patches so ROI/reference edits do not rewrite unrelated integration settings.

## Operation states

| Mode | Permitted behavior |
| --- | --- |
| Normal browsing | Visible-screen scheduled reads, optional small image refresh |
| Editing config/ROIs | Keep draft state, no polling of full config, detect stale source before write |
| Stream active | Exclusive camera/HTTP session; no background polling or chunk loading; stop by removing/disconnecting stream element |
| Camera test/reference save | Serialize camera mutations, wait for completion, refresh only relevant previews |
| Upload/backup/export | Serial or very limited transfers, accurate progress/cancel state, no unrelated polling |
| OTA/reboot | Explicit state machine; stop all ordinary work; distinguish upload, checksum, scheduling, restart, reconnect |

Aborting a browser request does not roll back a server-side mutation. Uncertain completion needs inspection/recovery, not automatic repetition.

## INI configuration strategy

A simple key/value dictionary is insufficient. Existing syntax includes commented-out sections/parameters, repeated/per-sequence settings, whitespace-delimited ROI/reference rows, defaults, paths, optional values and unknown future keys.

Use a lossless line/token model plus typed field views and focused patches. Preserve unrelated lines/comments/unknown keys and original order. Derive a complete field inventory from `readconfigparam.js`, `edit_config_template.html`, parameter docs and firmware readers; documentation alone is not the schema. Keep raw editing available. Use small build-generated metadata/help loaded only where needed. Avoid bundling the 145 KiB MkDocs stylesheet or duplicating long help on every screen.

Save validation and readback reduce errors but cannot make delete/upload atomic; see [03-api-contracts.md](03-api-contracts.md). Do not design a "safe save" promise the existing backend cannot fulfill.

The user permits necessary focused firmware/API work. If reliable saves or native setup/status cannot be delivered well with existing routes, implement a small compatible extension with a documented contract and tests in the owning stage. Preserve old endpoints and clients; identify required firmware support in the alternative's compatibility metadata. Further permission is not required merely because this work changes firmware within the agreed scope. Broad recognition/integration redesign remains outside this facelift.

## Update/cache design

Keep application chunks content-addressed with short hashes. ETags and improved cache policy are required firmware work in stage 00b; see [08-cache-and-firmware-support.md](08-cache-and-firmware-support.md). HTML and other mutable static resources should revalidate using `Cache-Control: private, no-cache` with `If-None-Match` / bodyless 304 handling. Truly content-hashed assets can use long-lived caching. Dynamic readings/configuration/images need an explicit freshness policy and must not inherit long static caching.

Retain a versioned entry URL as migration/recovery for browsers holding the previous firmware's 12-hour cache entry; new headers cannot recall an already fresh cached response. Catch missing lazy chunks/preload failures and offer one controlled reload, preserving unsaved work before leaving. Test old cached entries and pages that stay open during an update. Apply intended HTML cache improvements to both UIs without changing legacy routes or workflows; this is an agreed firmware change, not a fallback proposal.

Keep alternative build/version metadata separate from legacy `html/version.txt`; its value drives existing firmware/HTML checks. Publish the firmware compatibility range or baseline for the alternative without overwriting legacy identity. A version probe alone is not proof that all optional capabilities exist.

OTA/remote/manual packages contain both UIs. Generate raw static output, do required text processing, then gzip once with deterministic metadata. Avoid running legacy tooltip/source mutation tools over generated alternative files. An upstream package lacking `/html/ui` will remove the alternative; documentation and reinstall instructions must say so.

## Development and testing

Use sanitized fixtures for ordinary frontend work and a local same-origin proxy for deliberate device testing. The user has a spare ESP32-CAM; plan staged validation there rather than on the production meter. Connection details, preparation and actual results are still pending. No real credentials, config secrets or meter history belong in committed fixtures. Keep write actions explicit in proxy mode; opening a preview must not trigger mutations.

Test parser/serializer fidelity, request scheduling, mutation failure/recovery, calibration transforms and ZIP/package behavior where these can lose data or overload the device. Use browser checks for layout, navigation, keyboard/touch and error states. Hardware smoke/soak and update/cache validation remain required; mocks do not replace them.
