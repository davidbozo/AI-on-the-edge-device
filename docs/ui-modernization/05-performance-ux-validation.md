# Performance, UX and verification

## Reproducible asset baseline

Measured locally on 2026-10-20 at source baseline `cdc8450924e9338ab564c3fd76ad998e7b7cbb84` using Python 3.14 `gzip.compress(data, compresslevel=9, mtime=0)`. Values are **bytes**, not KiB. They are estimates from repository input, not browser wire captures or the exact CI gzip output.

| Input | Raw bytes | Gzip level 9 bytes |
| --- | ---: | ---: |
| `index.html` | 11,419 | 3,285 |
| `overview.html` | 8,816 | 2,200 |
| `style.css` | 3,327 | 929 |
| `common.js` | 4,516 | 1,434 |
| `readconfigcommon.js` | 7,226 | 2,167 |
| `readconfigparam.js` | 43,084 | 6,544 |
| `jquery-3.6.0.min.js` | 89,503 | 31,010 |
| `firework.js` | 2,339 | 1,068 |
| `firework.css` | 741 | 346 |
| `plotly-basic-2.18.2.min.js` | 976,530 | 327,442 |
| `jszip.min.js` | 97,642 | 28,530 |
| `edit_config_template.html` | 150,683 | 17,449 |
| `mkdocs_theme.css` | 145,209 | 20,668 |
| `edit_reference.html` | 56,158 | 8,856 |

There are 63 files under `sd-card/html`, totaling 2,219,730 bytes; 30 top-level HTML inputs and 113 Markdown parameter pages. These counts include source templates and an existing gzip asset; they are not the packaged transfer size. Tooltips expand generated configuration/reference HTML, so their source gzip sizes understate release page size.

The unique direct code/style/page set for legacy shell + overview in the table is 48,983 gzip bytes (about 47.8 KiB). It excludes icons/logo, dynamic ROI image, HTTP headers, API responses and generated differences. Shared browser caching can avoid repeat transfers; the observation is not "every navigation downloads all libraries". The graph adds roughly 320 KiB gzipped for Plotly alone, which is a more substantial target than changing an already small page's HTML.

Reproduce individual inputs without changing source:

```powershell
@'
from pathlib import Path
import gzip
root = Path('sd-card/html')
for name in ['index.html', 'overview.html', 'jquery-3.6.0.min.js',
             'plotly-basic-2.18.2.min.js', 'jszip.min.js']:
    data = (root / name).read_bytes()
    print(name, len(data), len(gzip.compress(data, compresslevel=9, mtime=0)))
'@ | python -
```

Stage 00 should record a release-generated baseline when tooling is available, with its commands/tool versions. Do not run the current tooltip generator over a working source tree casually: it writes source files and uses shell `cp`.

## Proposed budgets to validate

Targets, not measured achievements or promises:

| Metric | Initial target | Measurement boundary |
| --- | --- | --- |
| Cold dashboard application transfer | <= 30 KiB gzip | Entry + required JS + CSS, excluding camera images/API/header bytes |
| Initial JS / CSS / HTML | <= 24 / 5 / 1 KiB gzip | Sub-budgets totaling the 30 KiB target |
| Initial static requests | Prefer 3, <= 4 | Entry, main JS including Preact, one CSS, optional small icon; count module preloads/shared imports too |
| Lazy feature group | Aim <= 45 KiB incremental gzip | All additional code/CSS for one group; justify measured exceptions |
| Navigation code requests | Prefer 0 for ordinary screens, 1 for justified heavy tools | HTTP/1.1: count every prerequisite/shared chunk and extra CSS/help request; more need explicit measurement |
| All alternative application code/CSS/HTML | Aim <= 256 KiB gzip | Includes chart/ZIP/digest dependencies; report help/images separately |
| Concurrent app fetches | <= 2 ordinary reads; 1 write workflow | Browser chunks/images and other clients also consume server capacity |
| Hidden tab polling | 0 periodic requests after settling | Suspend timers and discard/cancel obsolete reads |
| Automatic image traffic | Only visible; after changed result or user refresh | No continuous camera capture on dashboard |
| Mutations automatically retried | 0 | Readback/recovery required for uncertain outcomes |

If full parity requires a larger specialist module, record the reason and measure before accepting a changed budget. Reducing initial load must not hide required features. Size targets exclude content images because those depend on camera/quality; **also report a complete cold-page total including the image and API traffic**, so excluded bytes do not hide real cost.

Use [09-bundling.md](09-bundling.md) to compare a whole application first, minimal heavy-library splitting, vendor/app separation and broader feature layouts with equivalent production code over HTTP/1.1. Count the full import graph, CSS/preload requests, dependency waterfalls, connection reuse and upgrade-cache behavior. If fewer requests justify a combined build above the 30 KiB initial-byte target, report the measured tradeoff rather than silently redefining the budget. The chosen layout is not yet an achieved result.

Do not set an arbitrary time-to-interactive promise without hardware/network measurements. Compare legacy and alternative on the same device, firmware, Wi-Fi conditions, meter data and browser. A faster browser render is different from reduced device work.

## Suggested refresh policy

- On entry, load small identity/readings first; load the ROI image afterwards when visible. Additional diagnostics belong on their screen or refresh less frequently.
- Start the dashboard with a conservative completion-based 30-second visible refresh for readings/status; explicitly show the age of the actual reading, not just fetch time. Benchmark against the legacy five-minute loop and tune to meter interval/device load. Smaller individual snapshots do not justify increasing total request traffic without measurement.
- Refresh round/low-priority health no more often than needed; use current result identity or explicit refresh for images. A round change does not by itself prove that every image is synchronized; verify the flow transition.
- History, full logs, config and archives load on demand. Default log/data tails are up to 80 KiB; offer manual refresh before any auto-follow mode.
- On error, retain last good values with stale status, back off safe reads (e.g. 5, 15, 30, then capped 60 seconds) and stop overlapping work. Show initialization differently from disconnect/auth errors.
- Revalidate HTML/mutable static assets through the required cache support in document 08. Measure conditional 304 body savings and validator lookup/heap/SD cost, not just compressed bytes. Live state/configuration should follow their explicit fresh-response policy rather than static caching.
- Streaming/OTA/calibration/file mutation pause ordinary polling. Stop stream by disconnecting the element before fetching another page or calling an API.

These are provisional defaults. Stage 13 must compare idle bytes/minute and request count to legacy, not only first-load bytes.

## UX acceptance

The user wants a simple facelift with familiar page purposes, labels/actions and workflow order; defer broader UX reimagining to their later review. Test at 320, 375, 768 and 1280 CSS pixels with keyboard, touch and 200% zoom on modern browsers. No document-wide horizontal scroll for ordinary screens; a bounded data table or image work area may scroll intentionally. Controls have clear labels, visible focus, adequate contrast, text error descriptions and approximately 44px touch targets for primary actions.

Always distinguish loading, unavailable, empty, stale, error and completed states. Show progress for long operations and preserve forms while a request fails. Separate "saved; restart required" from "applied" and "update scheduled" from "installed". Avoid success notifications before checksum/readback/completion. Distinguish device time, browser time and meter-reading timestamp.

ROI/reference editors need both source-pixel numeric inputs and pointer controls, accurate image fit/zoom mapping, keyboard nudges, useful touch handles and visible unsaved state. A responsive layout is incomplete if calibration only works with a mouse on a fixed 640px canvas.

History parity includes multiple sequences and all four series, selection/relative modes, missing data, chart legend/zoom/pan/reset/export and an accessible table alternative. Do not silently replace empty data with zero or connect invalid readings as valid samples.

## Verification layers

| Layer | What it establishes | Representative checks |
| --- | --- | --- |
| Static/build | Correct deployable output | Gzip decoding, no double compression, path/MIME compatibility, no external runtime URLs/source maps, budget report, no legacy file changes |
| Unit/contract | Data and operation correctness | INI round-trip/patches, known/unknown options, CSV/HTML adapters, decimal strings, status bodies, scheduling, stream/OTA state, image transforms |
| Browser with fixtures | UI behavior | Every F-row's actions, narrow/wide layouts, forms, navigation/history, keyboard/touch, abort/error recovery, cold/warm cache and missing lazy chunks |
| Firmware package inspection | Coexistence and update contents | Both UIs in OTA/remote/manual artifacts; legacy version/entry preserved, nested files gzip once, exact archive paths |
| Firmware HTTP/cache | Correct browser revalidation without stale state | ETag/304 bodies, gzip representation, no-cache/private/immutable policy, auth/setup/error handling and validator invalidation on updates/replacements |
| Actual ESP32-CAM | Serving/resource compatibility | Correct MIME/gzip for ESM, auth, socket pressure, stream disconnect/light release, writes/readback, camera/ROI results, reboot/update/cache behavior |
| Device soak | Sustained reliability | Multiple recognition rounds, stable heap trend, no watchdog/reset/SD errors, idle traffic, recovery after Wi-Fi loss, second client/legacy tab |

Use focused tests where failure can lose configuration, corrupt geometry or overwhelm the device; avoid snapshots that merely restate markup. Record unavailable tools/hardware as pending, never as passed.

## Hardware evidence sheet

The user has a spare ESP32-CAM. Use it for staged firmware/cache/camera/update validation after establishing its address, board/SD setup and applicable device-operation authorization. The production meter is not the default test target. Hardware availability is confirmed; no device run has yet been performed.

For each run record firmware/alternative build ID, board/camera/PSRAM, SD model/size, active integrations, browser/device versions, viewport, Wi-Fi RSSI, meter interval, dataset/sequence count, test commands and timestamps.

Collect legacy and alternative: cold/warm transferred bytes and request counts, load/usable time, idle bytes/requests per minute, image bytes, peak observed active requests, free/minimum/largest heap where instrumentation exists, recognition-round duration and errors/resets. Compare similar repeated runs, not a single best result. `/heap`, `/sysinfo` and logs can help; account for the measuring requests themselves. Do not claim minimum-heap or largest-block evidence if the deployed firmware does not expose it.

Exercise a second tab/client, enabled Basic auth, long/empty/missing logs, unavailable optional integrations, slow Wi-Fi, dropped upload, config edit in legacy while new UI is open, stream start/stop/navigation, fresh SD setup, full update and old cached entry loading new lazy assets. An overnight multi-round soak is a proposed final check; record actual duration and results.
