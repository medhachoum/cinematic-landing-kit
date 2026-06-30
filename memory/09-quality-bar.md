# 09 · The Quality Bar & Pitfalls (what gets rejected)

The reference is **TV-commercial / Apple × Cartier / Hermès** dark-or-light luxury — not ordinary web layouts. Hold this bar.

## Standards
- **Aesthetic:** cinematic lighting, **Smooth Scroll Storytelling**, **Dynamic Product Reveals** that "look like a TV ad, not a website." Everything dramatic and interconnected.
- The product is the hero of a **continuous, unbroken visual journey** tied to scroll — "the raw material gives birth to the product, the product fills its vessel, in one continuous flow the eye never leaves."
- **Typography:** clean over calligraphic for luxury. For Arabic: **El Messiri** (headings) + **Tajawal** (body); avoid **Amiri** for big display. High contrast with the background.
- **Captions over film:** small, high-contrast, **off-center (right in RTL)** so they don't fight the centered subject. The opening centered title is fine; subsequent center text is not.
- **Nav:** a beautiful header WITH the product logo that **hides on scroll-down, returns on scroll-up** (removing it entirely is wrong).
- Generate **real assets** (images AND video) via Higgsfield — don't lean on placeholders or reuse rejected assets.

## Auto-rejects — never do these
- Janky/choppy scroll, frame loss → caused by scrubbing `video.currentTime`. Use a canvas frame sequence ([02](02-scroll-film-canvas.md)).
- A visible **white/black rectangle** around a product image → `mix-blend-mode` broke under a GSAP transform. Use a transparent cutout ([04](04-cinematic-hero.md), [05](05-theming.md)).
- A **static image + fade-in** as the hero or any "feature" beat → must be cinematic (entrance + continuous life + pointer depth + pinned reveals). "Just an image and sections with fade-out" is the single most common complaint.
- Visible cuts/seams between clips; cross-dissolving two different stills (ghosting).
- Generic text+image grids; big centered display text overlapping the visual; low-contrast text.
- Filler eyebrows like "A CINEMATIC JOURNEY".
- An empty intro splash before the product; awkward large empty gaps from over-tall sections.
- Non-modest people; product identity drift; a brand mark rendered in the wrong place (e.g. on the product when it belongs on the packaging).

## Working method
- Decide the **theme + story** from the product and its reference photos FIRST, then build.
- **Verify generated assets by looking at them** (identity, modesty, branding placement, scale) BEFORE animating or spending video credits.
- When the client's feedback is blunt, treat the harsh wording as **signal** — each rejection usually contains a precise, correct fix. Apply it, don't argue.
- Keep iterating the top of the page hardest; the first impression carries the whole experience.
