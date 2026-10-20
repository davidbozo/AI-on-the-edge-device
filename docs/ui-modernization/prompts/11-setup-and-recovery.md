# Stage 11 — Full SD-card setup and recovery

```text
Execute stage 11 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, camera/ROI/config/maintenance handoffs and the full setup.html/explain/edit_explain source. Distinguish the SD wizard from softAP.cpp's embedded provisioning server.

Implement the native guided SD setup sequence: start, stream/mounting, reference capture, alignment markers, digit ROIs, analog ROIs, full configuration and completion. Reuse the new feature components/services; avoid parallel wizard-only implementations. Preserve back/next/skip/abort/restart behavior, help and unsaved state. Completion must successfully persist System.SetupMode=false before deliberately rebooting; failures must remain recoverable. Do not redesign the embedded AP or USB webinstaller.

Add setup/uninitialized/unavailable/critical recovery presentation using verified contracts, including documented support implemented in stage 00b. A nested index bypasses root's server-side setup/error interception and the original firmware lacks a clean global status/capabilities API. If existing/00b support is still insufficient, implement a small compatible structured endpoint with contracts/tests rather than guessing state or leaving parity gaps. Keep direct root recovery and explicit setup entry. No mutation on detecting/entering a setup state. Recovery/setup responses need the cache policy in document 08, not a static index ETag.

Finish contextual help/version compatibility and error paths for F29,F30. A fully native workflow is required; launching legacy wizard/config/ROI pages cannot satisfy parity. Verify complete, skipped, aborted, resumed/restarted and failed-save sequences with fixtures, keyboard/touch and narrow layout. Keep fresh-SD/hardware checks pending if not performed.

Update feature evidence, any added API/compatibility contracts and handoffs/11-setup-and-recovery.md. Next: 12-packaging-and-coexistence.md.
```
