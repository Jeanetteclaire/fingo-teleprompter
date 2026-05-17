# Build log

Significant milestones for `fingo-teleprompter`. Not every commit — just the moments where a real piece of work landed and is worth remembering.

Newest at the top.

---

## 2026-05-17 — Step 1: Camera background

Front camera live feed shows as a blurred fullscreen background after a "tap to begin" prompt. Three error states designed and styled: permission denied (with retry), no camera, browser unsupported. Camera mirrored (selfie behaviour) and fades in on the `playing` event to avoid a flash of unblurred video.

Decisions:

- Tap-to-enable rather than auto-init. Safer across iOS Safari quirks; pattern also sets up the eventual start button.
- Blur level: 4px (started at 8px, tuned down against real filming conditions).
- Typography is system font for now — placeholder. Real typography lands in step 2.

State: Single `index.html`, deployed via GitHub Pages. Camera is the only working feature; tapping does nothing beyond initialising it. No PWA manifest yet (step 7).
