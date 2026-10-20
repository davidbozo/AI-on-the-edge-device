# Stage 03 — Dashboard and recognition

```text
Execute stage 03 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, relevant handoffs and the overview/recognition contracts in documents 01-05.

Replace F01-F04 workflows in native alternative screens: all sequence readings/raw/previous/error, device identity/version compatibility, flow status/round, device date/time, temperature/RSSI/uptime, latest ROI image, recognition details and intentional manual-start action. Use /json for shared readings; do not reintroduce four separate /value calls when a snapshot suffices. /json alone does not contain absolute change or full ROI details: preserve recognition parity from the verified HTML/image contracts without embedding legacy pages.

Load small text data first, image afterwards when visible. Show actual reading age, last refresh and stale/loading/initializing/unavailable/auth states accurately. Keep last good data during a failed refresh. Use the shared scheduler, pause on hidden tabs and avoid image requests unless results changed or user requests them. Treat the proposed refresh interval as adjustable and report bytes/requests, including a full page total with the image. Do not claim synchronization between round and image without evidence.

Verify multi-sequence, invalid/raw-N, empty, initializing, disconnected, partial-status and manual-start rejection scenarios with fixtures. Check phone/wide layouts, keyboard navigation and back/forward. No config, OTA or file writes on load. Build and report initial code/request budget.

Update F01-F04 evidence without treating unfinished ROI editors as complete detailed-recognition parity. Write handoffs/03-dashboard.md. Next: 04-history-and-diagnostics.md.
```
