# Stage 07 — Camera, stream and alignment

```text
Execute stage 07 of docs/ui-modernization/06-work-plan.md. Read prompts/README.md, shared context, stage 06 handoff, legacy reference/alignment pages, verified camera query mapping and CaptureToStream firmware code.

Implement native F06-F08: supported camera/LED/zoom/rotation controls, test capture/reference save, previews, two alignment reference markers/crops, enhancement and test-align behavior, plus stream with light on/off. Preserve all distinct existing sensor controls and documented restrictions. Use focused config patches and the verified file/crop operations; do not copy arbitrary long filenames into short firmware buffers.

Build one reusable source-image coordinate transform for fitted/zoomed canvas views. Separate source pixels, CSS display pixels and device-pixel ratio. Provide numeric fields, keyboard nudges and touch controls; no fixed-width-only editor.

Streaming is an exclusive blocking HTTP session. Load its modules/controls first; pause ordinary fetches and camera writes; show a visible stop; disconnect/remove the stream source before attempting API stop/navigation work. Teardown must run on route changes, hidden/unmounted state and cancellation as appropriate. Aborting the client does not prove that lighting has already stopped: verify on hardware when available.

Test coordinate transforms, focus/drag/numeric synchronization, supported camera option mapping, async capture/error/save states, crop files and stream lifecycle. Keep actual camera/config/file writes out of automatic fixture checks. If device access is available and authorized for these actions, record stream disconnect/light release and resulting reference/alignment files; otherwise keep device gates pending.

Report module size and request behavior; write handoffs/07-camera-and-alignment.md and F06-F08 evidence. Next: 08-roi-editors.md.
```
