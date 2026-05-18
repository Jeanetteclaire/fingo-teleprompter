# Build log

Significant milestones for `fingo-teleprompter`. Not every commit — just the moments where a real piece of work landed and is worth remembering.

Newest at the top.

---

## 2026-05-18 — Step 7: PWA wrapper

`manifest.json` declaring the app (name, icons, standalone display mode, portrait orientation, dark background colour). `index.html` updated with iOS-specific meta tags: `apple-mobile-web-app-capable`, status bar style, app title, and `apple-touch-icon` links for the home-screen icon. Safe-area-inset padding added to the edit button, speed slider, and edit view so UI elements respect the notch and home indicator when running in standalone mode.

Custom icon: the mouth reference image at 192px and 512px, sitting in the repo as `icon-192.png` and `icon-512.png`.

Decisions:

- `display: "standalone"` — launches without Safari chrome (no URL bar, no tabs). Feels like an app.
- Short name "Fingo" — fits under the home-screen icon without truncation.
- Theme + background colour both `#0a0a0a` to match the app's dark identity through cold launch.
- No service worker. Offline support would require one but the teleprompter needs the camera (which itself needs HTTPS and a live page), so true offline isn't a real use case here. Skipped deliberately.
- No custom splash screen. iOS shows a brief dark frame on cold launch — adequate, not designed.
- 512px icon registered with `purpose: maskable` as well as `any`, so Android can crop it into its adaptive-icon shape if anyone installs on Android.

State: functionally and visually a real app on the home screen. Installable on iOS via Safari → Share → Add to Home Screen. Launches full-screen from icon. The mouth reference image now serves as the app icon.

---

## 2026-05-18 — Step 6: Wake lock

Wake Lock API requested when entering prompter view, released when returning to edit view. Re-acquires automatically when the tab becomes visible again after being hidden (browser releases on hide). Quiet fallback for browsers that don't support the API — nothing breaks, the screen just behaves normally.

Decisions:

- No visible indicator. Wake lock is invisible by design — works in the background.
- `wakeLockDesired` flag tracks intent separately from sentinel presence, so visibility-change handler knows whether to re-request after backgrounding.
- Wrapped in try/catch with console.error logging. Failure shouldn't block the prompter from running.

State: screen stays awake during prompter use, sleeps normally otherwise. Functional, no UI surface.

---

## 2026-05-18 — Step 5: Speed slider

Slider at the bottom of the prompter view controls scroll speed (15-90 px/s, step 5, default 45). Dims to 35% opacity while actively scrolling so it stays available without competing visually. Chosen speed persists to localStorage.

Decisions:

- Range 15-90 px/s. Natural reading speed lands near the middle — wider than typical use but gives headroom either side without forcing it.
- Native HTML `<input type="range">` with custom-styled track and thumb via Webkit and Firefox shadow-DOM pseudo-elements.
- Slider sits above the tap zone in z-index so adjusting it doesn't trigger pan-scroll.
- No numeric readout — thumb position is the indicator.

State: scroll, speed control, manual scroll-back, script entry, save/clear/history all working. Remaining: wake lock, PWA wrapper, countdown + illustrations, polish.

---

## 2026-05-18 — Step 3.5: Save, clear, and script history

Save and clear buttons below the textarea. Save adds the current script to a 5-slot FIFO history stored in localStorage; oldest falls off when full. History items display the first non-empty line of the script as their label; tapping one loads it back into the textarea. Clear empties the textarea and active script without confirmation.

Decisions:

- Save dedupes against the most recent entry. Repeated saves of the same script don't create duplicate history entries.
- History and active script use separate localStorage keys (`fingo:script-history` and `fingo:active-script`). Keeps the two concerns independent.
- Save/clear/start-prompt all share the same "muted when textarea empty" pattern.
- Edit view is now scrollable on smaller phones if textarea + actions + history don't all fit at once.

State: Functionally complete for typing, pasting, and managing scripts. Scroll mechanics in step 4 already in. Speed control, countdown, wake lock still pending.

---

## 2026-05-18 — Step 4: Auto-scroll with manual scroll-back

Auto-scroll engine via `requestAnimationFrame`. Tap anywhere on the screen to start; tap again to pause; tap to resume. Swipe up/down to manually scroll for finding a particular line. Tap/swipe disambiguation uses a 10px movement threshold. Swiping while auto-scrolling pauses it. Scrolling past the end enters an "ended" state; one more tap resets to the top.

Decisions:

- Tap zone covers the whole screen except the edit button. Pattern works well for teleprompters because your hand is already on the phone.
- First line of text positioned at ~35% from top of screen. Above centre — eyes naturally read upper portion when phone sits near the camera lens.
- Default scroll speed: 30 px/sec. Felt slightly slow on first use; range gets a slider in step 5.
- Pointer events (rather than touch events) for gesture handling. Works across iOS/Android/desktop with the same code.

State: Scrolling is complete. Speed is fixed at 30 px/s.

---

## 2026-05-17 — Step 3: Paste-in input

Textarea on the dark edit view replaces the hardcoded placeholder paragraph. Whatever you paste becomes the prompter text. Active script auto-saves to localStorage continuously; survives a refresh. "Tap to begin" pill is muted when the textarea is empty. Edit button in the prompter view returns to edit view for swapping scripts.

Decisions:

- Camera initialises once and stays running; returning to edit doesn't tear it down.
- localStorage key: `fingo:active-script`. Reserved for the active script only — history (step 3.5) gets its own key.
- Textarea inherits Manrope but smaller (18px) — for typing comfort, not prompter reading.

State: `index.html` has the full visual identity plus working script entry. No scrolling yet.

---

## 2026-05-17 — Step 2: Typography and text overlay

Manrope loaded from Google Fonts. Hardcoded placeholder paragraph renders centered over the blurred camera, with a dark tint layer between camera and text for legibility.

Decisions:

- Manrope at weight 500 (Medium), 44px. Slightly heavier weight chosen for reading without glasses at filming distance.
- Soft white (#f5f5f5), letter-spacing 0.01em, line-height 1.5.
- Width 85% of screen, max-width 600px, vertically centered.
- Tint opacity: 0.5 (started at 0.35, tuned up — at lower values the eye competed between the text and the blurred camera for focus).

State: `index.html` now has the full visual identity — blurred camera, dark tint, Manrope script overlay. Tap-to-begin still the only interaction.

---

## 2026-05-17 — Step 1: Camera background

Front camera live feed shows as a blurred fullscreen background after a "tap to begin" prompt. Three error states designed and styled: permission denied (with retry), no camera, browser unsupported. Camera mirrored (selfie behaviour) and fades in on the `playing` event to avoid a flash of unblurred video.

Decisions:

- Tap-to-enable rather than auto-init. Safer across iOS Safari quirks; pattern also sets up the eventual start button.
- Blur level: 4px (started at 8px, tuned down against real filming conditions).
- Typography is system font for now — placeholder. Real typography lands in step 2.

State: Single `index.html`, deployed via GitHub Pages. Camera is the only working feature; tapping does nothing beyond initialising it. No PWA manifest yet (step 7).
