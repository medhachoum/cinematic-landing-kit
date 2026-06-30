# 08 · Preview & Environment Gotchas

Hard-won quirks that otherwise waste hours. (Windows-flavored, but most apply anywhere.)

## Missing CLI tools — use substitutes
- No **`jq`** → parse JSON with `grep -oE` (see [06-higgsfield-pipeline](06-higgsfield-pipeline.md), parse `result_url` specifically).
- No **`ffmpeg`/`ffprobe`** → use **Python + OpenCV** (`cv2`) to read videos, get frame count/fps, extract & resize frames. Guard against `fps==0` / unreadable files (a failed download = a missing or 0-frame mp4).

## Serving the static file for preview
Serve with `npx -y serve -l <port> .`. Some ports are OS-reserved — use e.g. 8123 with `autoPort:true`. The dev server tool reads a `launch.json` (see `templates/launch.json`).

## The headless preview tab is HIDDEN → rAF is PAUSED
This is the big one. In a backgrounded preview tab `document.hidden === true`, so `requestAnimationFrame` fires **0×**:
- **Time-based GSAP tweens never advance** (`gsap.ticker.frame` stays 0): entrance timelines, ambient color tweens, reveals — all frozen. `gsap.set()` (instant) still works.
- **`preview_screenshot` times out** (the renderer is suspended) — and also hangs when a `<video>` is buffering.
- **Bulk image preloading stalls** (a 90-frame canvas sequence stops at ~60%).
> This is the ENVIRONMENT, not a page bug. A real, visible browser runs everything fine. So:
> - **Trust `eval` over screenshots.** Verify structure, computed styles, no console errors, and force end-states via `gsap.set(...)` to confirm layout.
> - Verify scroll-driven (scrub) state by **driving Lenis programmatically**: `window.lenis.scrollTo(y, {immediate:true, force:true})` then `ScrollTrigger.update()`. Plain `window.scrollTo` does NOT update ScrollTrigger when Lenis owns scroll. Position by a trigger's pixel range `st.start + p*(st.end - st.start)`, not `offsetTop`.
> - To screenshot at all, pause/clear any `<video>` first, and accept that entrance/continuous animations won't show.

## `<picture>` 404 pitfall
If a matched `<source media=...>`'s image 404s, the browser does NOT fall back to `<img src>` — the `<img>` fires `error` and shows nothing. Don't point a `<source>` at an asset that may not exist yet; use a single `<img>` until the responsive crop is actually generated.

## Muted autoplay
Browsers (and the headless preview) may not autoplay video without a gesture. Call `video.play().catch(()=>{})` on the first `pointerdown/wheel/keydown/touchstart` and on the section's ScrollTrigger `onEnter`; keep a poster as fallback. Use a JS playlist (advance on `ended`/`error`) instead of one looped clip. **Do not set the `loop` attribute if you rely on the `ended` event** — a looping element never fires `ended`, stranding the playlist.

## Graceful asset fallback (CSS)
`::after` does NOT render on replaced elements (`<img>`). Put the fallback gradient on a **container** (`.fallback-host`), and on `img.onerror` hide the img + add `.is-missing` to the nearest `.fallback-host`. So a missing asset shows a tasteful gradient, never a broken-image box.
