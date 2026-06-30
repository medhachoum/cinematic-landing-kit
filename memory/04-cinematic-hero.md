# 04 · The Cinematic Hero (the make-or-break first impression)

The top of the page decides everything. **A static image + a fade-in is NOT acceptable** — it reads as "just another website" and will be rejected hard. The hero must feel alive and flow into the film below (one journey).

## Use a transparent product cutout, not a blend trick
Drop the product onto the page as a **background-removed transparent PNG** (Higgsfield `image_background_remover`) and composite it normally with a soft `drop-shadow`. Do NOT rely on `mix-blend-mode` for the hero — see the critical caveat in [05-theming](05-theming.md) (multiply/screen **break** on any element GSAP transforms → an ugly white/black box). A cutout is bulletproof on any background.

## The recipe (layers + motion)
Structure (nested wrappers so each owns ONE transform channel — no conflicts):
```
.stage (perspective)
  .aura            → gold radial light: CSS entrance fade + slow pulse + pointer parallax
  .motes           → ~8 drifting gold dust particles (CSS keyframes, varied delay/size)
  .hero-inner
    .latin / eyebrow
    .media-float   → CSS float (translateY bob)
      .media-tilt  → JS pointer-driven rotateX/rotateY (3D depth)
        img.cutout → GSAP entrance (scale/rotate/opacity)
        .specular  → moving light sheen, masked to the cutout silhouette:
                     mask: url(cutout.png) center/contain no-repeat;
    h1 (wordmark)  → GSAP clip-path reveal
    .sub
  .scrollcue
```

1. **Entrance timeline on load** (GSAP): cutout scales/rotates in, wordmark reveals via `clip-path: inset(...)`, eyebrow/sub/cue stagger in. ~1.5s, `power3.out`.
2. **Continuous life:** aura opacity pulse, gold dust motes drifting upward, gentle float, a light **sheen sweeping across the product** (a moving gradient `mask`ed to the cutout PNG so it only shows on the product).
3. **3D pointer parallax:** on `pointermove`, tilt the product (`rotateX/Y`) and drift the aura/motes the opposite way for depth. Reset on `pointerleave`.
4. **Scroll handoff:** fade + lift the whole composition as you scroll, expanding the aura — flowing into the film.

## Guards
- `prefers-reduced-motion`: disable continuous loops + entrance; show the final visible state.
- If GSAP fails to load (CDN blocked): elements that start at `opacity:0` for the entrance must be force-revealed in a `if(!hasGSAP)` fallback, or they stay invisible.
- Keep the wordmark from overlapping the product (flex-stack, verify no overlap). Keep total hero content within the viewport so there's no awkward empty gap (a reduced hero height ~130vh, not 175vh).

> The same energy applies to any "environmental" section (e.g. an origin/heritage beat): make it **full-bleed + pinned** with a slow Ken Burns push, a growing light glow, and a masked line-by-line text reveal — not a small framed image with a fade.
