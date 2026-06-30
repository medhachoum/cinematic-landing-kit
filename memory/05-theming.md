# 05 · Theming — Light vs Dark, and the Blend Trap

The color world follows the PRODUCT and its source photography. Decide light vs dark from the reference assets, then keep one coherent system.

## Decide the theme from the assets
- **Dark luxury** (espresso/black grounds, dramatic raking light): the product pops via **`mix-blend-mode: screen`** dropping the BLACK background of dark-bg assets onto the dark page. Generate assets on a near-black seamless ground.
- **Light gallery** (warm ivory/sand grounds, soft daylight, lots of negative space — Apple × Hermès × Aēsop): the product pops via **`mix-blend-mode: multiply`** dropping the WHITE background of white-bg assets into the paper. Generate assets on a seamless warm off-white ground (e.g. `#FBF8F2`).
- If the reference product is photographed white-on-white (common for "art object" products), **light is usually the right call** and the real reference photos can be dropped straight onto the page via `multiply` — the page looks finished before you generate anything.

## ⚠️ THE BLEND TRAP (cost real time — read this)
**`mix-blend-mode` (multiply OR screen) BREAKS on any element that GSAP transforms.** A `transform` creates a new stacking context, so the element blends against white/black instead of the page → an ugly visible rectangle.
- Symptom: a sharp light/dark rectangle around an image that should have vanished into the background. (Multiply can only darken / screen can only lighten — if you see a box the wrong direction, blending is OFF.)
- It works on non-transformed product shots (e.g. a reveal image with no GSAP transform on the `<img>` itself) and fails on transformed ones (e.g. a hero image GSAP scales/parallaxes).
- **Fix:** use a **background-removed transparent PNG** for anything you animate (especially the hero). Reserve `mix-blend-mode` for static, non-transformed product shots only.

## Ambient mood per section
Keep a fixed `#ambient` background-color layer and tween it on each section's `onEnter`/`onEnterBack` (GSAP color interpolation, ~1.1s).
- **Light theme:** shifts are subtle WARMTH changes (ivory → sand → honey), reading like changing daylight — not light/dark swings. Text is dark ink everywhere → always legible.
- **Dark theme:** shifts in darkness/warmth (espresso → warm brown → near-black). Watch text contrast during the tween; if a section flips to a light ground, give it its own opaque surface so dark text never sits on a dark tween mid-transition.
- The CTA "dim" becomes a soft warm-white **wash** in light themes (not a black overlay).

## Light-theme palette that worked (adapt the hues to your product)
`--paper:#FBF8F2; --sand:#ECE2CF; --cream:#F3EEE2;` · text `--ink:#241812` (headings) / `#6E5C4B` (body) · gold must be DEEP to read on light: `#A97B33 → #8A6128` (pale gold disappears on white). Pull the product's own colors (e.g. caramel/mahogany) for accents.
