# Stage 12 — Combined packaging and legacy coexistence

```text
Execute stage 12 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, all prior handoffs and the actual build.yaml/static-serving/ZIP-extraction code.

Integrate the alternative static production output into release staging under html/ui while preserving every legacy page, generation step, version.txt identity, default entry and setup/error behavior. Include both UIs in relevant OTA, remote-setup and manual-SD artifacts. Keep the source project separate from sd-card/html; do not commit generated bundles into legacy source unless an agreed repo policy requires it.

Ensure text substitution/legacy tooltip tools never corrupt compressed alternative output. Gzip exactly once with deterministic metadata, supported logical URLs/MIME types, short hashed paths and bounded preload fan-out. Use locked dependency installation and report the selected toolchain. Inspect nested archive paths/directory creation assumptions; test extraction/package structure without flashing hardware.

Inspect the complete emitted import/preload/CSS graphs against document 09, not just filenames in the entry HTML. Verify specialist libraries stay behind intended lazy boundaries, no duplicate runtime is shipped and normal navigation reuses modules/cache. Include the complete chunk set in packages and record compressed bytes/request counts for representative first visits and an app-only update scenario.

Integrate stage 00b's validator generation/metadata into every install/update path after final text processing/compression. Add package assertions for both UIs, correct representation tags/metadata, invalidation/swap behavior, no .gz.gz, decompression/header compatibility, no external runtime URLs/source maps, byte/path budgets and preserved legacy artifacts. Compare legacy generated content excluding legitimate build metadata. Do not assume that a UI-only ZIP is additive: current OTA replaces the entire HTML folder.

Document the normal revalidated alternative URL, manual additive copy, complete combined OTA, reinstall after upstream OTA, rollback by returning to legacy and recovery if an old open app references missing chunks. Required ETag/no-cache firmware support must be included and its minimum version documented. Retain a versioned entry URL for migration from the previous firmware's 12-hour cache, not as a substitute for revalidation. Do not change the root default or add a legacy menu link without a recorded decision.

Run available build/package checks and report firmware build availability separately. Do not deploy/push/publish as part of packaging verification. Update F31,F32 and handoffs/12-packaging-and-coexistence.md. Next: 13-parity-and-device-qualification.md.
```
