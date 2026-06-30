# 03 · Seamless Transitions (no visible cut)

Goal: a continuous visual journey with **no visible cut** between stages (e.g. raw material → product forms → packaging seals → final reveal). The eye never leaves the subject.

## Rules
1. **Never cross-dissolve two DIFFERENT still images** to fake a morph — it produces an ugly double-exposure "ghost". Any morph/transformation = a **real video** clip.
2. **Boundary-matched clips:** generate each clip (image-to-video) with BOTH a `--start-image` and an `--end-image`. Make **clip N's end-image == clip N+1's start-image** (the SAME keyframe file). When the clips play/scrub back-to-back, the handoff frame is identical → invisible seam. When extracting the frame sequence, **drop the duplicated first frame of clips 2..N**.
3. **Match edges + background across ALL keyframes.** Prompt every keyframe with the identical background spec and an explicit edge color (e.g. "seamless warm off-white #FBF8F2 fading to the same tone at every edge and corner", or for dark themes "deep near-black #0B0805 at every edge"). Mismatched edges = a visible band when stitched.
4. **Bridge big composition jumps with an intermediate keyframe.** If two states are far apart (a full-screen object → a small one), insert a "gather"/transition keyframe and split into two clips rather than one hard morph.
5. Keep camera/motion **slow and locked**; prompt "no cuts, no flicker, slow motion, no morphing artifacts". For clips with people add "no face distortion" and keep motion subtle.

## Keyframe → clip pattern
```
K1 ──V1──▶ K2 ──V2──▶ K3 ──V3──▶ K4 ──V4──▶ K5
(image)   (image)    (image)    (image)    (image)
each Vn: --start-image K(n)  --end-image K(n+1)
```
Generate the keyframes first (image-to-image, identity-preserving), THEN the boundary-matched clips, THEN extract → one continuous frame sequence for [02-scroll-film-canvas](02-scroll-film-canvas.md).
