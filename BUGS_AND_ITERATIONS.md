# Bugs and Iterations — darkfire-null

Running log of every defect found, every iteration that landed, and the why
behind each. Newest at top. (CLAUDE.md rule #11 — the trail is institutional
memory.)

---

## 2026-07-08 — P2-4 asset diet: hero PNG → WebP (3.3MB → 456KB)

**Problem:** `fairy-sleeping.png` (the hero image, 1024x1536) shipped as an
uncompressed 3.3MB PNG — the single largest asset on this one-page site by
a wide margin, on every page load.

**Root cause:** No compression pass was ever run on the source art before
committing it; PNG is the wrong format for a photographic/painted hero image
with no need for lossless fidelity or transparency.

**Fix:** Converted to WebP at q=80 (`cwebp -q 80`) — 3.3MB → 456KB (~7x
smaller), same 1024x1536 resolution, verified via `dwebp` round-trip decode.
Swapped the single `<img src>` reference in `index.html` and deleted the now
dead `fairy-sleeping.png` (confirmed zero remaining references via grep
before deletion). Verified the site still serves correctly: `python3 -m
http.server` + `curl` confirmed `index.html` returns 200 and
`fairy-sleeping.webp` returns 200 with `content-type: image/webp` at the
expected byte size.

Went with a direct format swap (no `<picture>`/PNG-fallback) — WebP has been
supported in all evergreen browsers since 2020 and this is a small personal
art page, not a broad-compat production surface. Per Rule Zero, the simplest
correct move.

**Files:** index.html, fairy-sleeping.png (deleted), fairy-sleeping.webp (added)
**Commit:** (this commit)
