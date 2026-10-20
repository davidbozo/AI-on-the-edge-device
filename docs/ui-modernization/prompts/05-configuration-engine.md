# Stage 05 — Lossless configuration and recovery

```text
Execute stage 05 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, prior contracts/API handoff, 03-api-contracts.md and 04-architecture.md. Read firmware config consumers and both legacy configuration helpers, not only parameter docs.

Implement a lossless INI line/token model with typed field views and focused patches. Support enabled/commented sections and values, sequence overrides/defaults, ROI/reference rows, original order/comments/whitespace where untouched, paths/optional values and unknown keys. Enumerate all legacy ordinary/expert fields and dependencies in maintainable metadata; the 113 documentation files are not the whole schema. Do not regenerate a dictionary that silently drops unknown data.

Implement save/recovery services using verified contracts. Retain original/draft bytes, support local original download, compare fresh server content before writing, validate first, serialize mutation operations, verify readback and surface uncertain/failed stages. Never reboot or report successful save on failed upload/readback. Existing delete + raw upload is not atomic; document its race/power-loss limits. If a focused configuration replacement/concurrency endpoint is needed, the user allows it: implement/document/test that compatible extension, including invalidation of any relevant validator metadata, before consuming it. Do not claim power-loss atomicity without filesystem evidence. Keep older-firmware fallback or a clear compatibility requirement explicit.

Use focused tests: byte-preserving no-op round-trip; isolated field/ROI patch leaves unrelated data unchanged; disabled sections, unknown fields, per-sequence precedence and raw edits; stale-source conflict; deletion succeeds but upload fails; readback mismatch and recovery. Synthetic fixtures must not contain real secrets. Config/form state stays in memory unless the user deliberately downloads it.

Do not build the whole settings UI in this stage. Update fidelity findings and metadata inventory, write handoffs/05-configuration-engine.md and record any needed design decision. Next: 06-configuration-ui.md.
```
