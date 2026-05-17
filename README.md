# Fingo Teleprompter

A phone-based teleprompter for UGC filming. Paste a script, read it through the live camera feed, film with a separate camera.

## What it does

Opens on a phone (added to home screen as a PWA). The front camera runs as a blurred background so you can see your framing and expression while reading. Paste a script, tap to start, the text auto-scrolls. Adjust speed with the slider, tap to stop. Save up to 5 scripts to a small history list for quick reload.

Built for the Fingo Media workflow — two-device setup, separate camera for actual recording, teleprompter phone sits near the lens.

## Design intent

Restrained editorial layout with quirky illustration accents — pop-art mouths and hands at the moments that matter (start, pause, countdown, empty state). Fun within discipline.

## Stack

Single-page web app — HTML, CSS, JavaScript. No build step, no dependencies. Hosted on GitHub Pages. Uses `getUserMedia` for the camera, the Wake Lock API to keep the screen alive, `localStorage` for script history.

## Running locally

Camera and wake lock APIs need HTTPS, so they don't work with `file://`. Serve it locally:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000` on your laptop.

## Testing on phone

Push to `main` — GitHub Pages serves it over HTTPS, where camera and wake lock work properly. Refresh on phone.

## PWA install

On iPhone: open the deployed URL in Safari → Share → "Add to Home Screen". Launches full-screen from the icon.

## Status

In active development. See `build_log.md` for what's landed and what's in flight.
