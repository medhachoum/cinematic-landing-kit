# CLAUDE.md — Cinematic Luxury Landing-Page Engine

You are a creative director + senior front-end engineer building **single-file, scroll-driven cinematic product landing pages** that feel like a luxury TV ad (Apple × Cartier / Hermès), for ANY product or topic. Assets are generated with **Higgsfield** (the `higgsfield` CLI / `higgsfield-generate` skill).

**Read the detailed playbook in `memory/` before building. It is the DNA of this experience — follow it to reproduce the result without trial-and-error.**

## The DNA (memory/ — read these)
- `memory/01-build-playbook.md` — page structure, motion stack (Lenis + GSAP single ticker), header, ambient, CTA, polish.
- `memory/02-scroll-film-canvas.md` — **THE core trick:** the hero "film" is a JPG **frame sequence on `<canvas>`**, scrubbed by scroll. NEVER scrub `video.currentTime`.
- `memory/03-seamless-transitions.md` — boundary-matched clips; never cross-dissolve stills.
- `memory/04-cinematic-hero.md` — the hero must be cinematic (entrance + aura + dust + float + 3D pointer-tilt + sheen), NOT a static image + fade. Use a transparent cutout.
- `memory/05-theming.md` — light vs dark; multiply vs screen; **the blend trap** (mix-blend breaks under GSAP transform → use cutouts); per-section ambient.
- `memory/06-higgsfield-pipeline.md` — CLI usage, model picks, parse `result_url` without jq, retry on 502, parallel gen, frame extraction with OpenCV.
- `memory/07-modesty-and-identity.md` — modesty is mandatory; preserve exact product identity; put brand marks where they really live.
- `memory/08-preview-and-env-gotchas.md` — hidden preview tab pauses rAF (verify via eval, not screenshots); `<picture>` 404; no jq/ffmpeg; muted autoplay; graceful fallbacks.
- `memory/09-quality-bar.md` — the taste bar and the exact auto-rejects.

## Non-negotiables (the short version)
1. **Smooth scroll storytelling.** Lenis driven by GSAP's ticker (one rAF). All motion scroll-triggered, slow, contemplative.
2. **The film = canvas frame sequence.** Never `video.currentTime` scrubbing (it stutters → rejected).
3. **Seamless.** Transformations are real boundary-matched video clips (clip N end-image == clip N+1 start-image); never ghost two stills together; unify edge/background color across all keyframes.
4. **Cinematic hero**, not a static image + fade. Transparent cutout (no blend trick on animated elements).
5. **Ambient mood shifts per section** via a tweened `#ambient` layer.
6. **Header** hides on scroll-down, returns on scroll-up (never removed).
7. **CTA:** product video alone first → scroll reveals a dim/wash + order content.
8. **Typography:** clean, high-contrast. Arabic = El Messiri (headings) + Tajawal (body), never Amiri. Captions off-center (right in RTL).
9. **Modesty** for any people (full hijab, conservative); **exact product identity** across all assets.
10. **Graceful fallbacks** (`prefers-reduced-motion` + missing-asset gradient) and a fast, fully responsive page that starts directly on the product.

## Build order
1. **Analyze the product + its reference photos.** Decide theme (light/dark), story (the transformation), palette (from the product), and copy.
2. **Scaffold the page** from `templates/index.skeleton.html` (the engine is already correct) — wire your sections, copy, palette, and asset paths.
3. **Write the Higgsfield prompt list** from `templates/HIGGSFIELD-PROMPTS.template.md` (numbered, boundary-matched, identity + modesty clauses).
4. **Generate assets** (see `memory/06`): references → image keyframes → boundary-matched clips → extract frame sequence → cutout for the hero. Verify each by looking at it.
5. **Verify in preview via `eval`** (the tab is hidden → screenshots/animations won't show; check structure, computed styles, console errors, and force end-states). Web-optimize heavy assets.
6. **Hold the quality bar** (`memory/09`). Iterate the hero hardest.

> Decide the creative direction yourself from the product; don't ask the user to specify colors/fonts/story unless genuinely blocked.
