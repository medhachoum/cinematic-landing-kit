# 02 · The Scroll "Film" = a Canvas Frame-Sequence

The single most important technique. The hero transformation must read as one continuous, butter-smooth film tied to scroll.

## The rule
**NEVER scrub a `<video>` with `video.currentTime = progress * duration`.** H.264 must decode from the nearest keyframe on every seek → stutter and dropped frames. This is the #1 thing that makes a "cinematic" page feel cheap. It will be rejected.

## The fix (Apple-style)
Convert the video(s) into a **numbered JPG frame sequence** and draw the current frame onto a `<canvas>` based on scroll progress. Pre-decoded image swaps = zero seek lag.

## Recipe
1. **Extract frames** from the source clip(s) — sample ~24–30 frames/clip, resize to ~1280px wide, JPEG q≈80. Concatenate clips into one continuous `f000.jpg … fNNN.jpg`. ~90–120 frames ≈ 5–8 MB total → preloads fine. (No ffmpeg needed; use Python + OpenCV — see [06-higgsfield-pipeline](06-higgsfield-pipeline.md).)
2. **Preload** all frames into `Image[]`; show a % preloader; promote to "ready" at ~60% loaded OR all-settled, with a **safety timeout** (~9s) so a stalled frame can't hang the page. If 0 load → show a static fallback still.
3. **Scrub** with one ScrollTrigger:
   ```js
   const filmST = ScrollTrigger.create({
     trigger:'#film', start:'top top', end:'bottom bottom', scrub:true,
     onUpdate: s => { if (ready) draw(Math.round(s.progress * (N - 1))); /* + drive captions */ }
   });
   ```
4. **`draw(i)`** uses object-cover math; cache `cur` to skip redundant draws; set `canvas.width/height = clientW/H * min(2, dpr)` and **redraw on resize**. Fill the canvas with the **edge/background color of the frames** first (so full-bleed edges never show a seam).
5. **On becoming-ready, draw the frame for the CURRENT scroll position** (`filmST.progress`), not frame 0 — otherwise frames that finish loading mid-section paint a wrong still until the next scroll.

## Captions over the film
Small, high-contrast, **off-center** (right side in RTL). Drive their opacity from the same scrub `onUpdate` using a `smoothstep` window per caption (`data-a`/`data-b` progress range). Don't center text over the subject (except the opening title).

## Why full-bleed canvas also fixes "cropped/seam" complaints
A full-bleed canvas on a stage whose background matches the frames' edge color → no visible image border, no letterboxing.

Pairs with [03-seamless-transitions](03-seamless-transitions.md) (how to make the source clips continuous before extracting).
