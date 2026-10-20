# Stage 04 — History, logs and system information

```text
Execute stage 04 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, relevant handoffs and the history/log/info source and contracts.

Implement native graph/table/log/diagnostics workflows for F19,F20,F22,F28. History needs date and sequence selection, value/previous/rate/absolute-change traces, relative mode, series visibility, useful equivalent zoom/pan/reset/export controls and an accessible data view. Preserve missing/invalid readings and decimal/timestamp semantics; handle unavailable dates and large files. Choose a small measured plotting approach without sacrificing recorded parity. Do not eagerly import Plotly or a large chart framework into the shell.

Load historical files on demand; no fetching every day automatically. Share the file/list adapter with the later manager. For current data/logs use bounded tail endpoints, manual refresh/full-file access, safe text rendering and clear error/empty states. Log severity highlighting must preserve readable spacing and not force-scroll while someone reads older lines.

System info should show all supported legacy fields, using /sysinfo for shared fields and limited on-demand metadata calls for the rest. Show unavailable optional information rather than inventing a value. Keep diagnostics traffic off ordinary dashboard polling.

Test CSV variants from stage 00, missing/empty files, malformed values, multiple sequences, log content containing markup, graph interactions, keyboard and narrow layouts. Build and record incremental chart/diagnostic gzip sizes plus request counts. State full historical-file manager dependencies that remain for stage 09.

Write handoffs/04-history-and-diagnostics.md and feature evidence. Next: 05-configuration-engine.md.
```
