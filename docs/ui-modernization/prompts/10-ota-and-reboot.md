# Stage 10 — OTA, reboot and reconnect

```text
Execute stage 10 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, file operation handoff, ota_page.html/reboot_page.html, server_ota.cpp and relevant upload/MD5 contracts.

Build a native maintenance screen with the full existing ZIP/BIN update workflow: file/filename checks, exact 8000*1024-byte limit, deliberate staging preparation, raw upload with meaningful upload progress, local/server MD5 comparison, scheduling install, reboot and bounded reconnect. XHR is acceptable where upload progress is needed; integrate it with shared operation control rather than requiring fetch everywhere. Inspect and preserve additional user-visible model-update behavior where present in the action inventory.

Document 09 records that the wire contract uses MD5, absent from crypto.subtle's standard digest algorithms, and crypto.subtle is restricted on ordinary HTTP origins. Use a small tested implementation compatible with that contract; a new npm package or full crypto suite is not implied. Apply the user's dependency policy, assess existing/replacement/focused implementation options and ask with concrete size/correctness/maintenance evidence if the tradeoff is unclear. Benchmark small checksum code in the app before adding another HTTP/1.1 request. A digest algorithm change needs compatible firmware/browser-origin behavior and is not automatic. ZIP extraction/install remains firmware-side.

Implement an explicit state machine. Upload completed, checksum matched, install scheduled, device restarting and version confirmed are different states. Freeze unrelated polling; do not automatically retry mutation GETs or uploads after uncertain completion. Validate text bodies even on HTTP 200. Never claim installed solely because an endpoint returned 'reboot'.

Provide reboot-only flow, useful timeout/recovery controls, legacy-root escape and return to the normally revalidated alternative entry. Preserve unsaved work before leaving. Model old pre-upgrade cached entries and open-page/missing-chunk behavior using stage 00b's ETag/cache contracts. Use existing/new documented firmware status support; add focused compatible support only if required, never assume a nonexistent endpoint.

Test checksum mismatch, oversized file, prepare/upload failure, dropped response after schedule, offline/reconnect timeout, version mismatch and success sequence using fixtures. Keep test updates off real devices unless the current task explicitly authorizes a concrete device update; hardware update evidence may remain pending. Do not change package/OTA firmware behavior in this stage.

Update F26,F27 evidence and handoffs/10-ota-and-reboot.md; report lazy digest/update code cost. Next: 11-setup-and-recovery.md.
```
