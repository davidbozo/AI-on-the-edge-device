# Feature inventory and parity checklist

The required release is a complete alternative to the SD-card UI. A native replacement may reorganize screens, but every current user workflow must remain available. A link/iframe to legacy is a development or recovery aid and does not satisfy a replacement row.

Status vocabulary: `planned`, `implemented`, `mock-verified`, `device-verified`, `blocked`, `not-applicable-with-reason`. All rows start **planned**. Change status only with a path to evidence in the progress/handoff records. Expand rows into action-level checks in stage 00; F01-F32 are the source-derived starting inventory, while F33 records the user's added caching requirement. This is not a claim of exhaustive runtime validation.

| ID | Current feature and source | Alternative acceptance requirement | Stage | Status |
| --- | --- | --- | --- | --- |
| F01 | Menu, page memory, hostname, firmware/HTML versions: `index.html`, `common.js` | Responsive navigation, direct hash links/back/forward, device identity, compatibility status, legacy-root escape; use separate storage keys | 01,03 | planned |
| F02 | Overview: `overview.html` | All sequences, value/raw/previous/error, flow status/round, device date, temperature, RSSI, uptime and ROI image; refresh and stale/error states | 03 | planned |
| F03 | Recognition details: `/value?full` | Equivalent reading/ROI/debug information in the new screen; inspect generated HTML before designing its parser/model | 03,08 | planned |
| F04 | Manual start: `/flow_start` | Show availability and actual success/rejection; cannot bypass inactive-flow restrictions | 03 | planned |
| F05 | Previous value: `prevalue_set.html` | Select sequence, read/set previous value, use raw-value shortcut, show rejected/unknown sequence responses | 06 | planned |
| F06 | Live stream with/without light: menu, `/stream` | Start both modes, visible stop, disconnect before navigating, lights released, other requests paused | 07 | planned |
| F07 | Reference image/camera: `edit_reference.html` | Capture/test/save reference, supported camera and LED controls, zoom/rotation/preview, parameter help, persisted configuration and files | 07 | planned |
| F08 | Alignment: `edit_alignment.html` | Both reference markers/crops, positions, enhancement, alignment test and settings, original/processed previews and save behavior | 07 | planned |
| F09 | Digit ROIs: `edit_digits.html` | Sequence/ROI create/select/rename/delete/reorder as supported, coordinate editing, aspect/sync/spacing controls, image overlay, save | 08 | planned |
| F10 | Analog ROIs: `edit_analog.html` | Sequence/ROI operations, coordinate/aspect controls, counterclockwise and supported angle options, image overlay, save | 08 | planned |
| F11 | Configuration modes: `edit_config_template.html` | All supported ordinary/expert fields and enabled/commented settings, validation, save/reboot-required feedback and contextual help | 05,06 | planned |
| F12 | Raw configuration: `edit_config_raw.html` | View/edit/download raw INI, preserve unknown data, unsaved-change handling and recoverable save errors | 05,06 | planned |
| F13 | Capture/alignment/model config | `TakeImage`, `Alignment`, `Digits`, `Analog`: settings and available model selection equal legacy | 06,07 | planned |
| F14 | Postprocessing config | All `PostProcessing` options including per-sequence overrides and previous-value behavior | 05,06 | planned |
| F15 | Integration config | MQTT/Home Assistant, InfluxDB v1/v2, Webhook enabled state and every existing field; do not add fabricated connection tests | 06 | planned |
| F16 | GPIO config | Existing pin/mode/name/interrupt/PWM settings and relevant dependencies; preserve firmware restrictions | 06 | planned |
| F17 | Timer/logging/system config | `AutoTimer`, `DataLogging`, `Debug`, `System`, timezone help and model lists; maintain actual restart semantics | 06 | planned |
| F18 | Home Assistant discovery resend | Availability based on MQTT/discovery config and compile-time capability where known; scheduled-send feedback | 06 | planned |
| F19 | Data graph: `graph.html` | Date/sequence selection; value, previous, rate and absolute-change series; relative display; series visibility; equivalent useful zoom/pan/reset/export controls | 04 | planned |
| F20 | Data table/files: `data.html` | Latest tail, full current file, historical data browsing/download; preserve missing vs empty values | 04,09 | planned |
| F21 | CSV export: `data_export.html` | ZIP download of data files, progress, bounded sequential requests, cancel/error reporting | 09 | planned |
| F22 | Logs: `log.html` | Current tail, severity highlighting, refresh/full log and historical files; text rendered safely, no forced scroll while reading | 04,09 | planned |
| F23 | File server: firmware HTML + `file_server.js` | Browse/up/download/upload/delete/delete-directory-contents, readonly display behavior, existing size/path restrictions and overwrite handling | 09 | planned |
| F24 | Configuration backup: `backup.html` | Browser-created ZIP of config files, accurate progress and errors, equivalent filenames/content; no accidental directory fetches | 09 | planned |
| F25 | Configuration restore: `backup.html` | Upload individual files through the new file manager, with explicit replacement/recovery. Legacy has no ZIP restore wizard | 09 | planned |
| F26 | OTA: `ota_page.html` | ZIP/BIN selection, exact size limit, prepare/upload/progress/MD5 validation/install/reboot/reconnect; distinguish scheduled from installed | 10 | planned |
| F27 | Reboot: `reboot_page.html`, `/reboot` | Deliberate restart, feedback, bounded reconnect and safe return to the alternative | 10 | planned |
| F28 | System information: `info.html` | All available firmware/build/chip/SD/network/heap details and relevant documentation links; loaded on demand | 04 | planned |
| F29 | SD initial setup: `setup.html`, `explain_*`, `edit_explain_*` | Guided stream/reference/alignment/digit/analog/configuration sequence, back/skip/abort/restart/finish; complete `SetupMode=false` save before reboot | 11 | planned |
| F30 | Help, compatibility and recovery | Parameter help equivalent to generated legacy help; timezone lookup; firmware/UI compatibility; setup/unavailable/corrupt-response states and direct legacy recovery | 01,06,11,13 | planned |
| F31 | Existing API clients/integrations | UI rewrite leaves MQTT, InfluxDB, Webhook, Home Assistant and existing REST/OpenMetrics/GPIO routes and payloads working | 12,13 | planned |
| F32 | Legacy coexistence and update survival | Legacy `/` stays default, both UIs ship in all relevant packages, legacy setup/error pages remain, rollback procedure works | 12,13 | planned |
| F33 | Required firmware caching improvement | ETags/conditional GET, bodyless 304, `no-cache` HTML/mutable resources, correct hashed/dynamic policies, auth/gzip/update/legacy checks | 00b,12,13 | planned |

## Scope boundaries

The wider device performs meter photography, image alignment, TFLite digit/analog recognition, postprocessing, SD data/image logging, MQTT/Home Assistant discovery, InfluxDB v1/v2, webhooks, GPIO control, REST/OpenMetrics and OTA. The rewrite supplies the UI for those capabilities; it does not reimplement the recognition firmware or alter integration protocols.

The user accepted Preact/TypeScript/Vite and a simple responsive facelift preserving familiar workflows. Broader page redesign waits for later review. Necessary small firmware changes and added endpoints are allowed, with caching improvements specifically required. Existing API/integration clients must keep working; new contracts and version requirements must be documented.

The embedded AP bootstrap in `code/main/softAP.cpp` and the separate `webinstaller/` are preserved external entry points. SD wizard parity (F29) is required. Redesigning the AP bootstrap or USB webinstaller is not currently requested. Runtime probes/recovery screens should be documented separately from normal parity; the current firmware does not provide a clean global capabilities/system-error JSON endpoint.

Model installation is reachable through existing file/OTA backend paths, even where the menu primarily presents model selection. Stage 00 must enumerate actual page controls and expose equivalent workflows where present. Do not confuse backend-only endpoints with user-visible features or silently expand scope to new features.

## Release gate

Every required row needs action-level coverage, browser verification and device evidence appropriate to the operation. An incomplete row blocks the full-parity release; a legacy link cannot waive it. For optional compile-time integrations, test enabled and unavailable behavior and record applicability rather than falsely marking an absent backend supported. New features such as ZIP restore, new authentication, cloud access or AP redesign require separate scope decisions.
