# Bundling and request-count strategy

Status: recommendation for discussion, updated 2026-10-21 with the user's dependency policy and HTTP/1.1 constraint. The user suggested dependency/application bundles to reduce HTTP requests; that specific split is not yet an accepted implementation requirement. Prefer fewer bundles and benchmark a combined application first. Validate any code-splitting benefit against actual production builds and the spare ESP32-CAM.

## Dependency policy

The user explicitly prioritizes bundle size, permits replacing all legacy dependencies with better-fitting approaches, and asks to avoid dependencies where a fairly straightforward implementation suffices. Preact is accepted; the feature inventory below does not preapprove any chart/ZIP/checksum package.

Begin with the exact required behavior and browser APIs/small helpers. For a nontrivial alternative, compare a focused custom implementation with the smallest suitable library, including plugins/transitive code. Record marginal production gzip bytes, request/cache impact, feature completeness, implementation effort and maintenance/correctness implications. Existing minified-library sizes are comparison baselines only; a package being small or familiar is not sufficient justification.

If the tradeoff is unclear, ask the user with the concrete options and measured/estimated cost before selecting/introducing the dependency. Continue work that does not depend on the answer. Do not invent a numerical approval threshold or require reconfirming the accepted stack. Do not omit parity requirements to make either option look better.

## HTTP/1.1 constraint

HTTP/1.1 supports persistent connections, so another asset request does not necessarily require a new TCP connection. Pipelined responses remain ordered; HTTP/1.1 lacks HTTP/2's independently multiplexed response streams. These are distinct from the firmware's own request handling and socket limits. See [RFC 9112, connection management](https://www.rfc-editor.org/rfc/rfc9112.html#section-9) and [RFC 9113, streams](https://www.rfc-editor.org/rfc/rfc9113.html#section-5).

This firmware allows five open sockets and performs synchronous file delivery in its handlers; inspect [server_main.cpp](../../code/main/server_main.cpp) and [server_help.cpp](../../code/components/jomjol_fileserver_ota/server_help.cpp). Extra assets add routing, headers, SD lookup/open work and possible dependency waterfalls even when a connection is reused. Five sockets do not establish that five large responses execute independently. Do not assume HTTP/2-style cheap fan-out or a fresh handshake for every asset.

This strengthens the case for merging small screens/helpers and avoiding automatic vendor/per-page/per-package chunks. Code splitting can still win when it avoids a substantial transfer on a slow link; HTTP/1.1 alone does not prove that downloading every specialist library upfront is fastest.

## Recommended starting point

Start the production comparison with one JS bundle containing Preact, application code and dependencies, plus one compact stylesheet: three initial static requests including HTML. Prefer keeping lightweight screens/settings/calibration helpers together so ordinary navigation does not fetch more code. Introduce a lazy boundary only where a substantial specialist dependency or unusually large feature saves enough transfer to justify the additional request. Charting and archive generation are the strongest initial candidates; this does not prescribe a lazy bundle for every functional area.

Camera images, API responses and optional icons are additional traffic and must be reported separately. A full-app candidate may exceed the provisional 30 KiB dashboard target: report that cost honestly, then compare a minimal heavy-library split. Preserve the target as an optimization goal without pretending every full-parity screen already fits it or waiving it silently.

| Logical group | Contents | Load time |
| --- | --- | --- |
| Application | Preact, navigation, dashboard, shared helpers and lightweight screens | Initial load; preferred combined bundle |
| History/chart | History/table/chart UI and plotting dependency | Merge lightweight UI; consider lazy loading substantial plotting code |
| Configuration | INI model, forms, field metadata and compact contextual help | Merge initially; separate only if measured size merits another request |
| Calibration | Camera/reference/alignment/digit/analog editors and common image geometry | Merge initially; separate only if measured size merits another request |
| Files/maintenance | File manager, backup/CSV archives, OTA/reboot and archive/digest dependencies | Merge lightweight tools; consider lazy loading substantial archive code |

These are functional areas to reason about, not five default emitted chunks. A tiny system-info screen stays with ordinary application code; a large archive library can stay behind an explicit export action. Record the measured bytes saved and critical-path/request cost for every extra chunk. One complete app plus one or two optional heavy-library bundles is a candidate to compare, not a fixed maximum regardless of full-app size.

Keep dependencies with the feature that uses them. A small common helper already needed on the dashboard belongs in core. A substantial dependency shared only by lazy groups may justify one shared lazy chunk. Avoid duplicate Preact instances, dependency cycles or a statically imported entry that drags every feature into initial load.

## Dependency/application split tradeoff

A separate dependency bundle can stay cached while application code changes, provided its bytes/hash remain stable. Its benefit is greatest when shared dependencies are large, needed immediately and application updates are frequent. It adds a file/request on a cold load, and the savings depend on actual build output and update patterns.

For this application, bundling the small core runtime with the app is the starting recommendation. A blanket `node_modules` bucket can mix the chart/archive/digest dependencies with Preact. Once core imports that shared file, it can transfer substantial specialist code before its screen is opened. Group based on actual use and the generated import graph; package origin alone is not a useful loading boundary.

One JS file for the entire full-parity UI is now the first benchmark candidate because HTTP/1.1 request overhead matters. Its cost is transferring all included tools on a cold dashboard load. Compare that cost against only the largest justified lazy splits before trying broader per-feature splitting. Several small modules per component/page/package are a poor starting point for this five-socket server.

## CSS, preloads and production output

Start with one compact external stylesheet and evaluate Vite's `build.cssCodeSplit: false`. This reduces per-feature stylesheet fetches but moves all extracted CSS into initial load; keep the existing initial CSS budget and measure it. If specialist CSS becomes substantial, allow a measured per-group stylesheet rather than hiding excessive CSS in the core budget. Avoid a separate CSS file per component and large theme/icon/font downloads.

Vite can split async CSS and preload common dependencies of dynamic imports automatically. Its documentation explains both the extra requests and why preloading can avoid sequential dependency waterfalls: [Vite features](https://vite.dev/guide/features.html#build-optimizations). Inspect generated HTML/module imports and browser requests, not just the count of source `import()` expressions.

Do not blanket-disable preloading without measurement: it can reduce simultaneous requests while increasing round trips. Keep initial imports small, avoid speculative loading of unopened screens, and limit a first specialist visit to one extra JS request where practical, or at most two when a shared dependency is justified. Start that load without unrelated background polls. Load every required control/dependency before starting the blocking camera stream.

Vite exposes CSS/preload and output options in [build configuration](https://vite.dev/config/build-options.html). Use APIs supported by the locked version; current and older Vite releases use different underlying chunk-configuration options. Do not paste an unverified `manualChunks` recipe or enable `preserveModules`. Development dependency prebundling is distinct from the deployable production build.

Keep CSS external initially so it can cache independently; inlining all JS/CSS into HTML removes requests but couples them to revalidated HTML transfer/update behavior. Small icons can be inline SVG. No runtime CDN, npm files served individually, browser import map to a CDN or server-side frontend runtime is needed.

## Cache and transfer behavior

Use short content-hashed JS/CSS names, gzip at packaging time, long-lived private caching for genuinely immutable asset URLs, and ETag/no-cache HTML revalidation as specified in document 08. Re-entering a loaded screen should reuse its already imported module; reloading the page can reuse cached static assets. Measure normal repeat visits and upgrades separately.

An HTTP 304 still involves a request. ETags reduce body bytes for revalidated resources; fresh cached hashed files can avoid network transfer/revalidation during their lifetime. An app/vendor split is therefore a potential update-cache optimization, not an automatic reduction in initial requests. Keep the old-open-app/missing-chunk recovery path even with good cache headers.

Static bundling does not combine `/json`, status/metadata requests or camera images. Keep application request scheduling and polling budgets separate. If small APIs dominate the total, evaluate a focused aggregate endpoint from an actual trace within the permitted firmware scope; do not make the JS bundle larger to solve API traffic.

## Complex features and likely runtime dependencies

Complexity does not necessarily require a library. Source inspection on 2026-10-21 identifies three areas that warrant comparison of custom/native and library approaches beyond the accepted Preact runtime:

| Feature and current source | Complexity to preserve | Dependency direction |
| --- | --- | --- |
| History graph: [graph.html](../../sd-card/html/graph.html) | Four series, mixed lines/bars, second Y axis, relative values, visibility, zoom/pan/reset/export interactions | Compare focused Canvas/SVG code with a small suitable chart library; include required interaction/plugin/adapter costs and ask if the tradeoff is unclear |
| Config backup and CSV archive export: [backup.html](../../sd-card/html/backup.html), [data_export.html](../../sd-card/html/data_export.html) | Multiple binary files/names, ZIP structure, progress/cancel, browser memory and download behavior | Evaluate a bounded writer for the actual archive requirements against a suitable ZIP library; native compression alone does not supply the container, and retaining JSZip is not assumed |
| OTA checksum: [ota_page.html](../../sd-card/html/ota_page.html) | Compute browser MD5 over upload bytes and compare to firmware digest before installation | A small tested implementation is needed for the existing protocol; this does not automatically require a new npm package or crypto suite |

Re-measured existing assets with Python gzip level 9, `mtime=0`: Plotly Basic 327,442 bytes (319.77 KiB), JSZip 28,530 bytes (27.86 KiB), MD5 3,494 bytes (3.41 KiB). These are current input-library estimates, not sizes for selected replacements or the future app. The checksum is small enough to compare inside the app bundle; it is not automatically worth an extra HTTP/1.1 request. Chart/archive code are much stronger split candidates.

Web Crypto's digest algorithms include SHA-1/SHA-256/SHA-384/SHA-512 rather than MD5, and `crypto.subtle` has secure-context restrictions: [Web Cryptography specification](https://www.w3.org/TR/webcrypto/). Do not assume switching to SHA-256 removes a JS dependency on ordinary HTTP device origins; algorithm changes need firmware compatibility and browser-origin support. This is checksum protocol compatibility, not a new cryptographic-authentication project.

The browser [Compression Standard](https://compression.spec.whatwg.org/) defines stream compression rather than a multi-file ZIP writer. ZIP headers/directories, filenames and checksums still need implementation. Determine the actual required archive subset and compare the effort/bytes of a focused tested writer with a suitable package; ask if the choice is unclear. The existing code's JSZip use generates a browser Blob; see [JSZip's generation API](https://stuk.github.io/jszip/documentation/api_jszip/generate_async.html). Legacy restore uploads individual files, so ZIP extraction/restoration is not required by parity. OTA ZIP extraction is firmware work, not a frontend archive parser requirement.

Other complex areas can stay small and application-owned:

| Feature | Why it is complex | Recommended baseline |
| --- | --- | --- |
| Reference/alignment/digit/analog editors | Source-pixel transforms, fitted/zoomed image, touch/keyboard interaction, ROI ordering/spacing/synchronization and persistence | Canvas or SVG plus Pointer Events and tested shared geometry; no general image-editor/scene-graph library initially |
| Structured/raw configuration | Commented sections/options, per-sequence settings, ROI rows, unknown-line preservation and recoverable writes | Custom lossless parser/patcher plus Preact/native forms; a generic INI parser or large form framework does not solve these device-specific rules |
| History CSV parsing | Dates, empty/invalid readings, trailing diagnostics and legacy variants | Focused parser for verified firmware output; evaluate a CSV library only if actual standard quoting/escaping/streaming needs justify it. The current writer concatenates fields, so a generic parser cannot reconstruct delimiters the producer failed to escape |
| File manager and downloads | Inert HTML-list extraction, paths, serial transfers, overwrite/recovery and cancellation | DOMParser, fetch/XHR, Blob/object URLs and download links; test target browser behavior before removing FileSaver |
| Setup/OTA/reconnect workflows | Async steps, pauses/exclusive operations, failures and recovery | Typed application state machines; no workflow/state-machine package required initially |
| Logs, date display, navigation and notifications | Formatting/filtering, device-vs-browser time, focus/accessibility and UI state | Preact, native controls, Intl and small helpers; no jQuery, date suite, data-grid or widget framework by default |
| Parameter help | Existing Markdown converted to help during packaging | Build-time conversion/compact metadata; no browser Markdown/highlighting dependency needed for parity |

Model inference, image alignment/cropping operations and integration publishing remain in firmware. The frontend does not need TensorFlow.js, OpenCV.js, a browser MQTT client or a video-player framework to replace their existing UI controls. The camera stream is an existing multipart JPEG endpoint; preserve its blocking/session behavior using native image display and teardown.

Evaluate any new dependency by required features, native/custom alternatives, marginal production gzip bytes including plugins/adapters, import graph/request cost, maintenance/license compatibility and behavior on ordinary HTTP. Development/build/test packages are not necessarily shipped runtime dependencies; inspect the production output. Apply the user's ask-if-uncertain rule before resolving an unclear tradeoff. No new package is selected or installed by this inventory.

## Measurement and decision gate

Provide the CLI workflow in [document 05](05-performance-ux-validation.md#early-cli-analysis-required-in-stage-01) with the first foundation build: normal Vite compressed-size reporting, a small built-in-Node size/request reporter and a proposed dev-only module visualizer. Record a real baseline before specialist dependency choices. Generated diagnostics remain outside deployed assets; use emitted gzip files for totals and module views for attribution.

Compare these production variants when representative code exists:

| Variant | Main benefit to measure | Cost to measure |
| --- | --- | --- |
| Whole application in one JS file | Minimum JS request count and compression across all code | Cold dashboard bytes and update download size |
| Initial dependency + application bundles | Stable dependency-cache reuse across app updates | Extra initial request and any eagerly included specialist code |
| Application + optional heavy-library chunks | Keep ordinary navigation bundled while omitting large chart/archive code from initial transfer | Extra first-use requests and any shared imports |
| Core + broader feature groups | Smaller dashboard if configuration/calibration code becomes substantial | More first-use requests, dependency waterfalls and entry-hash churn |

Use the same features, minifier/build target, gzip settings and cache conditions for comparisons. Report compressed bytes per initial/lazy graph, request count (including preloads and CSS), a browser waterfall, API/image traffic and behavior across a small UI update with unchanged dependencies. Count all first-visit prerequisites, not just the route chunk itself. Measure summed gzip outputs; separate files can compress differently from a combined file.

Stage 01 starts with a combined application and establishes the layout/report; it cannot prove full-app tradeoffs before feature code exists. Revisit with representative chart/configuration/calibration/archive code and during final qualification. Keep document 05's initial 30 KiB optimization target, three preferred static requests/four maximum and existing feature budgets; document a tradeoff if the fastest measured combined build exceeds that byte target. Prefer zero code requests for ordinary navigation and one for a justified heavyweight tool; further prerequisite requests need explicit measurements. No artificial stub comparison should be presented as a final bundle result.
