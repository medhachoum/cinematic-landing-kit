# 06 · Higgsfield Asset Pipeline (the CLI playbook)

How to generate every asset reliably via the `higgsfield` CLI (Higgsfield MCP / `higgsfield-generate` skill). Auth via `higgsfield auth login`; check with `higgsfield account status`.

## Model picks
- **`nano_banana_2`** (Nano Banana Pro) → reference-driven, **identity-preserving** product shots (logo / calligraphy / shape must stay identical). Pass the reference with `--image`. Params: `aspect_ratio` (1:1,3:2,2:3,4:3,3:4,4:5,5:4,9:16,16:9,21:9), `resolution` 1k/2k/4k. This is your workhorse for anything with the product in it. Also does re-skins (color/flavor variants) while keeping the same logo/text.
- **`gpt_image_2`** → standalone scenes/environments with NO product identity to preserve (landscapes, backdrops). `quality high`, `resolution 2k`.
- **`seedance_2_0`** → image-to-video. Flags: `--start-image`, `--end-image`, `--aspect_ratio` (16:9/9:16/4:3/3:4/1:1/21:9), `--resolution 1080p`, `--duration <int>`, `--bitrate_mode high`, **`--generate_audio false`** (underscore!). The flaky one — retry.
- **`image_background_remover`** → transparent cutouts (for the hero / floating product). Just `--image`.

## Submit pattern
```bash
higgsfield generate create <model> ... --wait --wait-timeout 25m --json > out.json
```
`--wait` blocks until done and the JSON contains the result. **The result is `result_url` (top-level).**

## Parse the result WITHOUT jq (jq is usually NOT installed)
The job JSON also lists the INPUT image URLs, so a naive "first URL" grab can return the input. **Extract `result_url` specifically:**
```bash
grep -oE '"result_url":[[:space:]]*"[^"]+"' out.json | grep -oE 'https://[^"]+' | head -1
```
Then download with a retry loop and verify the file exists & is non-empty:
```bash
for d in 1 2 3; do curl -fsSL "$URL" -o asset.jpg && [ -s asset.jpg ] && break; sleep 3; done
```

## Transient errors — RETRY, don't give up
Intermittent `HTTP 502` and "Cannot reach …cloudfront…/<uuid>.png" (backend couldn't fetch the just-uploaded input) are flaky, not misuse — re-running succeeds. **Wrap every generation in a 3–4× retry loop.** The CLI exit code can be 0 even when the download branch failed → always verify the output file.

## Reuse uploads
Pre-upload references once: `higgsfield upload create ref.jpg --json` → capture `id`. Pass that UUID to `--image <id>` on every later call to skip re-upload and dodge the flaky re-fetch.

## Run in parallel, in the background
Image gens are reliable; Seedance video is the slow/flaky one. Fan out: launch all image gens with `&` + `wait` in one background script; do the boundary-matched videos in a second pass (they depend on the keyframes). Verify each output, then retry only the failures.

## Frame extraction (Python + OpenCV — no ffmpeg)
After the clips land, extract one continuous sequence (drop the shared duplicate first frame of clips 2..N):
```python
import cv2, os, glob
clips = ["v1.mp4","v2.mp4","v3.mp4","v4.mp4"]; out="assets/seq"; os.makedirs(out, exist_ok=True)
for f in glob.glob(out+"/f*.jpg"): os.remove(f)
PER, W, idx = 24, 1280, 0
for ci, p in enumerate(clips):
    cap = cv2.VideoCapture(p); total = int(cap.get(cv2.CAP_PROP_FRAME_COUNT)) or 0
    if total <= 0: print("WARN empty", p); cap.release(); continue   # a failed download = 0-frame mp4
    picks = [round(i*(total-1)/(PER-1)) for i in range(PER)]
    if ci > 0: picks = picks[1:]                                     # drop the duplicate boundary frame
    for fr_i in picks:
        cap.set(cv2.CAP_PROP_POS_FRAMES, fr_i); ok, fr = cap.read()
        if not ok: continue
        h, w = fr.shape[:2]; fr = cv2.resize(fr, (W, round(h*W/w)))
        cv2.imwrite(f"{out}/f{idx:03d}.jpg", fr, [cv2.IMWRITE_JPEG_QUALITY, 80]); idx += 1
    cap.release()
print("frames:", idx)   # set FRAME_COUNT in the HTML to this number
```
(4 clips × 24, dropping 3 duplicates = **93** frames. Keep the HTML's `FRAME_COUNT` in sync with the real count.)

## Web-optimize before shipping
Generated stills can be 6–10 MB. Downscale display images to ~2000–2600px JPEG q≈87 (cv2) so the page stays fast. Keep full-res keyframes only as video sources. Videos load lazily (CTA only), but consider their weight.

See [03-seamless-transitions](03-seamless-transitions.md) for boundary-matching and [07-modesty-and-identity](07-modesty-and-identity.md) for the two hard prompt constraints.
