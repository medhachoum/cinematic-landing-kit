# Higgsfield Prompt List — TEMPLATE

Fill in `{{PRODUCT}}`, `{{BRAND_MARK}}`, `{{ORIGIN}}`, the palette/edge color, and the story beats.
Numbered by order of appearance. Types: Text-to-Image (`gpt_image_2`) · Image-to-Image (`nano_banana_2`) · Image-to-Video (`seedance_2_0`) · Background-Remove (`image_background_remover`).
All assets land in `assets/` with the exact filenames the HTML expects.

## 0 · Setup
- Save the client reference(s) under ASCII names, e.g. `assets/ref-product.jpg` (and `assets/ref-detail.jpg`).
- Pre-upload once and reuse the IDs: `higgsfield upload create assets/ref-product.jpg --json` → `--image <id>`.

### Shared spec (paste into every product keyframe)
- **Edge/background (theme-dependent, keep IDENTICAL across keyframes):**
  - Light: `Clean seamless warm off-white #FBF8F2 fading to the same paper tone at every edge and corner, soft natural daylight, gentle soft shadow.`
  - Dark: `Deep espresso near-black #0B0805 fading to pure black at every edge and corner, dramatic single warm raking light.`
- **Identity:** `Keep the exact {{PRODUCT}} identity and the {{BRAND_MARK}} unchanged.` (And: keep the mark where it really lives — on packaging vs on the product.)
- **Always:** `editorial luxury, hyper-detailed, no extra text, no watermark.`

---

## A · The transformation film (keyframes P1→P5 + clips V1→V4) — boundary-matched
> Beat list (edit to your product): raw/plain {{PRODUCT}} → it forms/assembles → the {{BRAND_MARK}} appears → final product → gallery reveal.

- **P1 / K1** — `assets/seq-src/k1.jpg` — i2i, ref, 16:9, 2k — *plain {{PRODUCT}}, hero pose.* + shared spec.
- **P2 / K2** — `assets/seq-src/k2.jpg` — i2i, 16:9 — *{{PRODUCT}} front-facing, centred.*
- **P3 / K3** — `assets/seq-src/k3.jpg` — i2i, 16:9 — *the transition state (forming / blank packaging).*
- **P4 / K4** — `assets/seq-src/k4.jpg` — i2i, 16:9 — *the FINISHED product with the {{BRAND_MARK}} in place.* (Often just the real reference, copied.)
- **P5 / K5** — `assets/seq-src/k5.jpg` — i2i, 16:9 — *final product on a pedestal in a soft gallery (reveal).*
- **V1** `assets/v1.mp4` — i2v — `--start-image k1 --end-image k2` — *slow orbit. Locked, slow motion, no cuts, no flicker.*
- **V2** `assets/v2.mp4` — `--start-image k2 --end-image k3` — *the form/assembly beat.*
- **V3** `assets/v3.mp4` — `--start-image k3 --end-image k4` — *the {{BRAND_MARK}} materializes (where it really lives).*
- **V4** `assets/v4.mp4` — `--start-image k4 --end-image k5` — *pull back to the gallery.*
- All clips: `--aspect_ratio 16:9 --resolution 1080p --duration 5 --bitrate_mode high --generate_audio false`.
- Then extract → `assets/seq/f000.jpg…` (OpenCV, drop duplicate boundary frames) and set `FRAME_COUNT` in the HTML.

## B · Section stills
- **P6** — `assets/origin.jpg` — t2i (`gpt_image_2`), 16:9, quality high, 2k — *{{ORIGIN}} environment, no product.*
- **P7** — `assets/product-hero.jpg` — i2i ref, 4:3/4:5 — *the product hero on the theme ground.* (Or use the real reference.)
- **P8 (opt)** — `assets/macro.jpg` — i2i ref — *extreme macro detail.*
- **P9** — `assets/ritual.jpg` — i2i ref, 16:9 — *lifestyle moment.* **If a woman appears: full hijab, conservative long sleeves, only face & hands (mandatory).** Realistic product scale.
- **P11/P12** — `assets/edition-1.jpg`, `assets/edition-2.jpg` — i2i ref re-skins/variants, keep the {{BRAND_MARK}}.

## C · Hero cutout + nav logo
- **CUT** — `assets/product-cut.png` — Background-Remove on the cleanest product/hero shot → transparent PNG for the hero (no blend trick).
- **P13 (opt)** — `assets/logo.png` — i2i "ONLY the {{BRAND_MARK}}, flat, on pure black/white" → Background-Remove → transparent nav logo.

## D · CTA + lifestyle video
- **V5** — `assets/cta.mp4` — i2v `--start-image k4 --end-image k4` (seamless loop) — *the product rotating slowly.* Poster = `assets/product-hero.jpg`.
- **V6** — `assets/life1.mp4` — i2v `--start-image ritual.jpg` — *subtle modest lifestyle motion.*

---
### Run notes
- `higgsfield generate create <model> ... --image <id> --wait --wait-timeout 25m --json`
- Parse: `grep -oE '"result_url":[[:space:]]*"[^"]+"' out.json | grep -oE 'https://[^"]+' | head -1` → `curl -fsSL` (3× retry).
- Retry 502 / input-fetch failures. Generate images in parallel (background); videos in a second pass.
- **Verify every asset by viewing it** (identity, modesty, mark placement, scale) before making video from it.
