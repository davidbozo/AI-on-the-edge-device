# Stage 08 — Digit and analog ROI editors

```text
Execute stage 08 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, stage 07 geometry/services/handoff and both complete legacy ROI pages/helpers. Use the action-level inventory from stage 00.

Implement native digit and analog editors with shared geometry but distinct supported controls. Preserve sequence/ROI creation, selection, rename/delete and supported ordering behavior; coordinate/dimension changes; digit synchronization, spacing and aspect controls; analog direction and other recorded options; overlays, previews and save semantics. Do not reduce parity to drawing one rectangle.

Keep coordinates in source-image pixels. Support touch/mouse/keyboard and numeric alternatives at narrow widths and zoom/DPR changes. Validate bounds/dimensions and ensure sequence/ROI names remain compatible with firmware and other settings. Changes must patch relevant rows and references without dropping unrelated unknown config or per-sequence settings.

Complete outstanding native recognition-detail workflows using verified reading/ROI image contracts. Lazy load editor code; avoid dozens of chunks/preview requests. Stop stream or conflicting camera operation before edits/saves.

Test transform/round-trip fidelity and multi-sequence edits, rename/delete implications, synchronized digit movements/spacing, analog options, empty/invalid ROIs, unsaved navigation and failed save/recovery. Compare action-by-action to legacy inventory. Real-device crop/recognition validation remains pending unless actually performed.

Update F03,F09,F10 evidence, build sizes and handoffs/08-roi-editors.md. Next: 09-files-backup-and-export.md.
```
