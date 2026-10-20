# Stage 13 — Full feature parity and device qualification

```text
Execute stage 13 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, every relevant handoff, action-level F01-F33 inventory, package instructions and 05-performance-ux-validation.md/08-cache-and-firmware-support.md.

Audit each user workflow against the legacy source and new implementation; run browser/contract/package checks and fix concrete parity, responsive, accessibility and resource issues. A native screen must handle its actual actions/errors, not merely exist. A legacy link/iframe or a mock screenshot is not parity. Verify all fields, graph controls, calibration tools, individual restore, backups, export, OTA/reboot and SD setup, plus optional integration unavailable behavior.

Measure actual production initial/lazy/all-code gzip sizes, static and API request counts, full-page bytes including images, warm navigation and idle bytes/minute. Compare against recorded legacy conditions. Check hidden-tab suspension, stream exclusivity, no repeated mutations and stale cached entry/new lazy assets. Report misses honestly and tune only with evidence.

Complete document 09's production comparison over HTTP/1.1: one combined application first, application plus minimal heavy-library splits, vendor/app separation, then broader feature splits if justified. Keep features/build/gzip settings equivalent. Report full import/preload/CSS waterfalls, connection reuse, first-use route requests and cache reuse across a small application update. Record why the final layout wins on the spare device, including any tradeoff against the provisional initial-byte target. Do not sacrifice parity to make a bundle smaller.

The user has a spare ESP32-CAM: once its connection/setup and operation authorization are established, use it for serving/auth/cache smoke and agreed intentional write/calibration/update tests. Verify ETag/304/no-cache, gzip representation, validator invalidation and normal/versioned entry behavior across updates. Record board/firmware/SD/browser/Wi-Fi/integration details, heap trend/recognition timing/logs, disconnect/reconnect, second client, stream light release, save/readback/recovery, fresh setup and combined update/cache behavior. Use a realistic multi-round soak and report actual duration. Do not use the production meter as the default test target.

If hardware or write/update authorization is missing, complete all independent fixes and offline verification, then document exact pending device gates. Do not mark release-ready, complete full parity, or fabricate device test results. Separate 'implementation complete' from 'device-qualified'.

Update every feature status with evidence, write handoffs/13-parity-and-device-qualification.md and a final acceptance/remaining-gates record. Preserve legacy as the default even if the alternative qualifies. No deployment/root switch/push is implied.
```
