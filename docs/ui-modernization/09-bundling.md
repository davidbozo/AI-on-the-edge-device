# Bundling and request-count strategy

Status: recommendation for discussion, updated 2026-10-20 after the user explicitly highlighted HTTP/1.1. The user suggested dependency/application bundles to reduce HTTP requests; that specific split is not yet an accepted implementation requirement. Prefer fewer bundles and benchmark a combined application first. Validate any code-splitting benefit against actual production builds and the spare ESP32-CAM.

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

## Measurement and decision gate

Compare these production variants when representative code exists:

| Variant | Main benefit to measure | Cost to measure |
| --- | --- | --- |
| Whole application in one JS file | Minimum JS request count and compression across all code | Cold dashboard bytes and update download size |
| Initial dependency + application bundles | Stable dependency-cache reuse across app updates | Extra initial request and any eagerly included specialist code |
| Application + optional heavy-library chunks | Keep ordinary navigation bundled while omitting large chart/archive code from initial transfer | Extra first-use requests and any shared imports |
| Core + broader feature groups | Smaller dashboard if configuration/calibration code becomes substantial | More first-use requests, dependency waterfalls and entry-hash churn |

Use the same features, minifier/build target, gzip settings and cache conditions for comparisons. Report compressed bytes per initial/lazy graph, request count (including preloads and CSS), a browser waterfall, API/image traffic and behavior across a small UI update with unchanged dependencies. Count all first-visit prerequisites, not just the route chunk itself. Measure summed gzip outputs; separate files can compress differently from a combined file.

Stage 01 starts with a combined application and establishes the layout/report; it cannot prove full-app tradeoffs before feature code exists. Revisit with representative chart/configuration/calibration/archive code and during final qualification. Keep document 05's initial 30 KiB optimization target, three preferred static requests/four maximum and existing feature budgets; document a tradeoff if the fastest measured combined build exceeds that byte target. Prefer zero code requests for ordinary navigation and one for a justified heavyweight tool; further prerequisite requests need explicit measurements. No artificial stub comparison should be presented as a final bundle result.
