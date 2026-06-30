# 07 · Two Hard Constraints for Generated Assets

## 1. Modesty (for Arab / Muslim audiences) — mandatory
Whenever people appear in/around the product (lifestyle, UGC, gatherings, iftar/Ramadan, hospitality), **any woman MUST be fully covered: an elegant hijab covering the hair, conservative long sleeves, tasteful and respectful — no skin beyond face and hands.** Put this explicitly in EVERY people prompt; non-modest output is an automatic reject.
- Prompt snippet: *"an elegant woman wearing a MODEST hijab that fully covers her hair, with conservative long-sleeved clothing, tasteful and respectful — no skin beyond face and hands"*.
- **Verify by reading the generated image before animating it.** Men / family / iftar / hospitality settings work well.

## 2. Preserve exact product identity
The product (logo, calligraphy/wordmark, shape, packaging) must stay **identical** across every asset — drift looks like a different product.
- Use `nano_banana_2` image-to-image with the product reference (`--image ref`) and state: *"keep the exact product identity / emblem / calligraphy unchanged."*
- This also powers **re-skins** for variants: *"re-skin into a deep ruby-red body, keeping the SAME emblem and calligraphy."*
- **Know WHERE the branding lives.** If a name/logo sits on the PACKAGING (e.g. printed on a box's glass panel), do NOT render it onto the product itself — generating a name engraved into the bare product looks fake and will be rejected. Keep the product clean; put the mark where it really is. Confirm placement from the reference (or ask the client) before generating.
- For a clean nav logo: generate the emblem alone ("ONLY the emblem, flat, on pure black/white") then background-remove.

> Both constraints go into the prompt text every time, and both are verified by **looking at the generated image** before spending video credits on it.
