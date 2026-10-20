# Existing API contracts and adapter plan

These contracts come from source inspection, not captured device responses. Stage 00 must validate payloads and extract sanitized fixtures. Keep root-relative API URLs independent of `/ui/`, and use same-origin requests with existing Basic authentication. A development proxy should preserve this origin arrangement; do not require disabling browser security.

Small necessary firmware/API additions are authorized, including required ETag/cache support in stage 00b. The endpoints below remain the current baseline until implemented changes are recorded. Keep new response schemas, errors, auth/limits and minimum firmware support clearly distinguished from existing contracts. See [08-cache-and-firmware-support.md](08-cache-and-firmware-support.md).

## Read endpoints

| Endpoint | Source-derived contract | Client handling |
| --- | --- | --- |
| `GET /json` | Object keyed by sequence name; all reading fields are strings. Requires created and initialized flow, otherwise 403. Can return an empty body | Preferred reading snapshot; explicit initializing/empty/malformed states |
| `GET /sysinfo` | One-element array with string fields `firmware`, `buildtime`, `gitbranch`, `gittag`, `gitrevision`, `html`, `cputemp`, `hostname`, `IPv4`, `freeHeapMem` | Normalize array once; avoid one request per field |
| `GET /info?type=...` | Plain text, 400 for invalid types | On-demand metadata and extra fields only |
| `GET /statusflow` | Plain text status/time or `Flow task not yet created` | Don't treat all 200 responses as initialized |
| `GET /info?type=Round` | Plain text round count | Refresh image when count changes if snapshot association is reliable |
| `GET /date`, `/uptime`, `/cpu_temperature`, `/rssi` | Plain text; RSSI may return 403 if Wi-Fi unavailable | Preserve display units and device/local-time distinction |
| `GET /starttime` | Startup timestamp text | Useful reboot discriminator; verify whether startup time is already SNTP-correct |
| `GET /value?all=true&type=value\|raw\|prevalue\|error` | Legacy text with sequence/tab/carriage-return formatting | Compatibility fallback only where needed; do not make four calls if `/json` suffices |
| `GET /value?full` | Firmware-rendered recognition details HTML | Extract required data; do not execute/import arbitrary markup/scripts into the new UI |
| `GET /img_tmp/raw.jpg`, `/img_tmp/alg.jpg`, `/img_tmp/alg_roi.jpg`, ROI names | Camera/recognition virtual images, may fall back to files | Lazy load; narrow cache-busting tokens for changed results |
| `GET /data`, `/log` | Tail of current data/message file, configured 80 KiB before skipping to the next newline | On-demand refresh; handle missing files and incomplete/empty tails |
| `GET /datafileact`, `/logfileact` | Full current data/message file | Explicit full download/open, no default polling |
| `GET /fileserver/<path>` | Raw SD file; directory URL ending `/` produces HTML table | Directory adapter parses inert HTML; file downloads preserve original bytes |
| `GET /editflow?task=namenumbers` | Tab-separated sequence names | Normalize empty/trailing fields deliberately |
| `GET /editflow?task=data` | Tab-separated data filenames | Date picker/file list, verify naming/order |
| `GET /editflow?task=tflite` | Tab-separated model filenames | Model picker, verify extensions and empty response |
| `GET /heap`, `/metrics` | Diagnostic text/OpenMetrics | On-demand diagnostics; UI does not redefine these public endpoints |

`/info` supports GitBranch, GitTag, GitRevision, BuildTime, FirmwareVersion, HTMLVersion, Hostname, IP, SSID, FlowStatus, Round, SDCardPartitionSize, SDCardFreePartitionSpace, SDCardPartitionAllocationSize, SDCardManufacturer, SDCardName, SDCardCapacity, SDCardSectorSize, ChipCores, ChipRevision and ChipFeatures. `FlowStatus` currently returns the literal `FlowStatus`, so use `/statusflow` for actual status.

Illustrative `/json` shape, synthesized from `ClassFlowPostProcessing::getJsonFromNumber` (not a captured reading):

```json
{
  "main": {
    "value": "123.456",
    "raw": "123.456",
    "pre": "123.450",
    "error": "no error",
    "rate": "0.006",
    "timestamp": "2026-10-20T12:00:00+0200"
  }
}
```

The timestamp string here is only illustrative: verify its format/timezone. Do not turn empty strings into zero, lose decimal precision in the displayed reading, or discard raw strings containing recognition placeholders such as `N`. The JSON lacks absolute change and per-ROI details found elsewhere. Keep the raw snapshot for support and normalize only fields whose contract is known.

CSV history uses the legacy column positions: timestamp, sequence, raw, value, previous value, change rate, absolute change, error, then digit/analog diagnostics. Verify `ClassLogFile::WriteToData` and actual fixtures, including commas/errors/header lines and older variants, before choosing a parser. `/json` alone cannot replace history or the full recognition view.

## Mutations and long-running actions

| Endpoint | Existing semantics | Required handling |
| --- | --- | --- |
| `GET /flow_start` | Requests start/wakes automatic flow; 403 when not active | User action only; show actual result, no automatic retry |
| `GET /setPreValue?numbers=<name>` | Reads previous value | Parse plain text and application error messages |
| `GET /setPreValue?numbers=<name>&value=<value>` | Changes previous value; negative value requests current raw value | Treat as mutation despite GET; `E9x` errors can arrive with HTTP 200 |
| `POST /upload/<path>` | Raw request body, **not multipart**; target must not exist | Explicit replacement sequence; exact size limit `8000*1024`; parent must exist |
| `POST /upload/firmware/<file>?md5` | Raw bytes; returns JSON-like `{"md5":"..."}` after upload | Compare to locally computed digest before install; verify content type independently |
| `POST /delete/<path>` | Deletes file, missing file tolerated; success may be 200/303 | Do not infer success from a final redirect alone; re-read/verify |
| `POST /delete/<directory>/?task=deldircontent` | Deletes files in directory; not recursive folder management | Deliberate action with exact target shown |
| `GET /editflow?task=copy&in=...&out=...` | Copies SD file, paths relative to `/sdcard`, short value buffers | Preserve supported names; verify destination before assuming success |
| `GET /editflow?task=cutref&in=...&out=...&x=...&y=...&dx=...&dy=...` | Cuts reference image, optional `enhance=true` | Calibration mutation; coordinate validation, inspect result |
| `GET /editflow?task=test_take...` | Camera test capture with many camera/zoom/LED parameters | Extract exact mapping from reference page and handler |
| `GET /editflow?task=cam_settings...` | Applies camera settings | Coalesce deliberate preview updates; avoid slider floods |
| `GET /editflow?task=test_align...` | Runs alignment test | Serial operation with clear completion/error handling |
| `GET /capture`, `/capture_with_flashlight` | Capture JPEG; uses camera resources | On demand; do not run concurrently with stream/calibration |
| `GET /save?...`, `/lighton`, `/lightoff` | Save capture/control light | Confirm exact save query and filenames in camera handler |
| `GET /stream` or `/stream?flashlight=true` | Blocking multipart JPEG response | Treat as exclusive session; stop by disconnecting client first |
| `GET /mqtt_publish_discovery` | Schedules discovery/static-topic publishing in next processing round | Optional compiled route; scheduled is not already published |
| `GET /ota?task=emptyfirmwaredir` | Clears staging files | Only as part of intentional update flow |
| `GET /ota?task=update&file=<name>` | ZIP/BIN schedules reboot-time install; TFL/TFLITE copies model into config | Validate returned text and separate schedule/install/reboot states |
| `GET /reboot` | Restarts device | User-triggered; then bounded read-only reconnect probes |

Legacy `/statusflow.html`, `/value.html`, `/wasserzaehler.html`, `/rssi.html`, `/cputemp.html`, `/setPreValue.html` aliases exist. The new UI can use current names; existing clients retain the aliases.

## Configuration save is not atomic

Legacy `SaveConfigToServer` deletes `/config/config.ini` then posts the replacement. The uploader rejects existing files and no atomic rename/replace endpoint is exposed. UI changes cannot promise transactional saving.

Proposed client behavior:

1. Retain original bytes and pending edits in memory; offer a local download before destructive replacement.
2. Re-read and compare with the originally loaded file before saving to detect another UI/tab's edits.
3. Validate and show changed fields; serialize writes through one operation controller.
4. Delete only after validation, upload replacement, fetch it back and compare bytes/content.
5. Keep a precise recovery state and retry/restore controls if upload or verification fails. Never reboot after a failed save.

A pre-save comparison reduces accidental conflicts but cannot eliminate the race with another client. A focused firmware replacement/concurrency endpoint is allowed if needed; implement and test it in the configuration stage while retaining compatibility. State guarantees precisely: staged writing and rename do not by themselves prove power-loss atomicity on FAT. Until that support exists, the frontend must remain honest about the delete/upload fallback.

## Directory listing adapter

`server_file.cpp` emits `table#files_table` rows containing a linked name, type, displayed size and optional delete form. It also emits page scripts, but a `DOMParser` result need not be attached to the document. Extract table rows/links as data, validate same-origin `/fileserver/` paths and directory flags, and discard scripts/forms. Do not fetch every link from the whole page: parent/navigation links are not backup/export files. The `readonly=true` query changes listing controls; it is not an authorization mechanism.

The file server hides/blocks reading `wlan.ini`. Preserve supported access boundaries; do not present it as downloadable config. Its upload/delete semantics differ by route/filename and need fixtures. The new UI should not introduce arbitrary folder creation or recursive deletion that the backend does not support.

## Adapter and error model

Keep transport modules free of UI imports. Normalize to explicit states such as `ready`, `initializing`, `unavailable`, `unauthenticated`, `not-found`, `malformed`, `network-error`, and operation-specific rejection. HTTP 403 can indicate flow/Wi-Fi initialization or operation refusal; Basic authentication uses 401. Do not treat every 403 as a password failure, or every HTTP 200 as operation success.

Use at most two concurrent ordinary application requests initially, no overlapping polls, completion-based scheduling, aborts on navigation and bounded backoff for safe reads. Mutations receive no blind retries; after a dropped connection, inspect state and show uncertainty rather than repeating an action. Streaming, calibration writes, file replacement and OTA have exclusive operation states. Browser asset requests are outside the fetch queue: the build must also keep preload/chunk fan-out bounded.

## Contract discovery still required

- Capture exact recognition HTML, CSV variants, directory HTML and application-level failure bodies.
- Enumerate every camera/test/save parameter, ROI option and supported field against handler and page source.
- Verify nested gzip ESM/CSS requests on the target firmware, actual path limits and query handling.
- Verify light/stream disconnect behavior, auth across fetch/images/proxy, and optional integration availability.
- Record how setup/critical initialization is exposed. If current contracts are insufficient, implement a small structured endpoint rather than guessing state; document/version it before use.
- Check upload redirects/MD5/error behavior and nested ZIP extraction with real package fixtures.
