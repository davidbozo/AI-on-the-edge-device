# Stage 09 — File manager, backup, individual restore and CSV export

```text
Execute stage 09 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, file/INI adapters and relevant handoffs, server_file.cpp, file_server.js, backup.html and data_export.html.

Build a native responsive file manager: directory/up navigation, downloads, raw upload, deliberate file deletion and supported delete-directory-contents behavior, readonly display and existing path/size restrictions. The backend does not provide arbitrary recursive folder management. Parse only verified directory table data; do not execute listing scripts or follow every link. Preserve wlan.ini access boundaries. Existing uploads reject files already present: make replacement explicit, retain recoverable original data where appropriate and show readback/errors.

Complete historical data/log file workflows from stage 04 using this one shared manager. Add configuration ZIP backup and data-folder ZIP export with locally bundled archive support, sequential/bounded reads, progress, cancel and accurate incomplete-file/error behavior. Compare inline vs lazy archive dependency placement per document 09's HTTP/1.1 strategy. Preserve file bytes/names; exclude directory/navigation links. The current restore feature uploads individual files: implement that native workflow. A new ZIP restore wizard is extra scope and must not be silently added.

Archive creation runs in the browser, not on ESP32. Apply document 09's dependency policy before choosing an archive package: determine the actual required archive subset, compare a focused tested writer with suitable small libraries, and implement directly where fairly straightforward. JSZip/FileSaver are comparison baselines, not requirements. If the tradeoff is unclear, ask the user with feature/effort/production-byte evidence before selecting/introducing a package. Native compression does not implement the ZIP container, and this UI does not need frontend unzip for OTA. Use native Blob/object-URL downloads where sufficient. If a sizeable library is justified, evaluate lazy loading with its actual gzip/request cost and bound export memory. Cancellation stops future work but does not undo completed server mutations.

Test listings with subdirectories/special names/missing files, upload 200/303/size/existing-file/partial failures, delete errors, overwrite recovery, backup completeness and archive contents, cancel and progress. Verify no credential/private-data fixtures and no unintended real writes.

Update F20-F25 evidence and handoffs/09-files-backup-and-export.md. Next: 10-ota-and-reboot.md.
```
