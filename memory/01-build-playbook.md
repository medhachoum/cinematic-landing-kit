# 01 · Cinematic Landing-Page Build Playbook

The proven structure + motion stack for a single-file, scroll-driven **cinematic** product landing page that feels like a luxury TV ad (Apple × Cartier / Hermès), not a website. Product-agnostic: works for any product or topic.

## Stack (single HTML file)
- **Tailwind (CDN)** for utility sprinkles + **custom CSS** in `<style>` for the real styling.
- **GSAP + ScrollTrigger** (animation + scroll triggers).
- **Lenis** (smooth scroll), driven by **GSAP's ticker** (one shared rAF clock — never two rAF loops):
  ```js
  lenis.on('scroll', ScrollTrigger.update);
  gsap.ticker.add(t => lenis.raf(t * 1000));
  gsap.ticker.lagSmoothing(0);
  window.lenis = lenis; // expose for verification
  ```
- Fonts: pick **clean modern** faces with high contrast. For Arabic use **El Messiri** (headings) + **Tajawal** (body) — NOT Amiri (too calligraphic). Latin accents: Cormorant Garamond.

## Page structure (top → bottom) that works
1. **HERO** — start DIRECTLY on the product, no empty splash. Must be *cinematic*, not a static image + fade. See [04-cinematic-hero](04-cinematic-hero.md).
2. **THE FILM** — a tall section (~600vh) with an inner `position:sticky; top:0; height:100svh` stage; scroll drives a continuous product transformation rendered as a **canvas frame-sequence**. See [02-scroll-film-canvas](02-scroll-film-canvas.md).
3. **PRODUCT REVEAL** — the hero product shot + specs + subtle parallax accents (editorial layout).
4. **RITUAL / MOMENTS** — a lifestyle / use-moment beat (people optional; see [07-modesty-and-identity](07-modesty-and-identity.md)).
5. **CTA** — a pinned section: product video plays **alone first**, then on scroll a dim/wash layer + order content fade in over it (build contrast before the ask).
6. **VARIANTS / EDITIONS** (optional) — re-skinned product cards.
7. Footer.

> Sections should be visually distinct but **connected** — transitions via a smoothly-tweened ambient background, never hard cuts.

## Fixed cinematic layers (z-stacked behind content)
- `#ambient` — full-screen background color, **tweened per section** (mood shifts). Animate the real `background-color` with GSAP (color interpolation), triggered on each section's `onEnter`/`onEnterBack`.
- `#glow` — soft radial light; opacity shifts per section.
- `#vignette` — subtle edge darkening to focus the center.
- `#grain` — faint film grain (SVG fractal-noise data-URI), `mix-blend-mode` + low opacity.
- `#progress` — thin gold scroll-progress bar at the very top.

## Header
Beautiful, contains the product emblem + wordmark. **Hide on scroll-down, return on scroll-up** (never remove it entirely):
```js
ScrollTrigger.create({ start:0, end:'max', onUpdate:self=>{
  progress.style.transform = 'scaleX(' + self.progress + ')';
  header.classList.toggle('solid', self.scroll() > 40);
  if (self.scroll() > 140) header.classList.toggle('hidden', self.direction === 1);
  else header.classList.remove('hidden');
}});
```

## Polish details that land
Film grain, thin gold progress bar, gold gradient text (`background-clip:text`), ambient color that shifts per section, soft radial glow behind the floating product, editorial section indices (٠١، ٠٢…), generous negative space.

## CTA reveal (smoothstep gate)
Tall pinned section; a scrubbed ScrollTrigger drives a `smoothstep`-gated reveal: video-only for the first ~third, then a dim/wash layer + content fade in. Cycle multiple short clips as a JS playlist (advance on `ended`/`error`).

## Non-negotiables
- All motion is **scroll-triggered**, slow, contemplative. Continuous ambient motion is OK only when it adds life (hero aura/float), never busy.
- Respect `prefers-reduced-motion` (+ IntersectionObserver fallback) and **graceful asset fallbacks** (a 404 must reveal a tasteful gradient, never a broken box).
- See [09-quality-bar](09-quality-bar.md) for the taste bar and the exact pitfalls to avoid.
