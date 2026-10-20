# Stage 06 — Complete configuration UI

```text
Execute stage 06 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, stage 05 engine/metadata/handoff, 02-feature-parity.md and original edit_config_template.html/prevalue_set.html/timezones.html.

Build responsive native forms for every supported ordinary/expert parameter in TakeImage, Alignment, Digits, Analog, PostProcessing, MQTT, InfluxDB, InfluxDBv2, Webhook, GPIO, AutoTimer, DataLogging, Debug and System. Support section/parameter enable state, per-sequence/default behavior, model lists, dependencies, raw INI editor, timezone lookup, changed-field display, unsaved-change guard and saved/restart-required feedback. Do not silently omit specialist fields to meet a size target.

Use the stage 05 lossless engine. Generate compact contextual help from existing parameter docs as appropriate, loaded with configuration rather than the shell; preserve help substance without shipping the whole MkDocs theme. Keep secrets from persistent browser storage and logs. Implement necessary missing APIs with documented contracts/tests if a real parity gap demands it; optional new connection-test product features remain outside the initial facelift.

Add previous-value read/set/raw shortcut with actual E9x rejection handling, and Home Assistant discovery resend with optional capability/config availability and scheduled-send feedback. Use exclusive operation control and recovery; manual saves/reboots must be deliberate actions.

Verify all field inventory entries have a UI binding or a documented supported raw workflow matching the legacy control, and do not count a raw-only escape as replacement of a legacy structured field. Test disabled/integration/per-sequence/unknown configs, save failure, unsaved navigation, model lists, previous value and discovery unavailable cases. Check narrow/wide/keyboard layouts and configuration lazy-bundle size.

Update F05,F11-F18 evidence and handoffs/06-configuration-ui.md. Next: 07-camera-and-alignment.md.
```
