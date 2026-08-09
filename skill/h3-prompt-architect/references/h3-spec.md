# MiniMax H3 — capability spec

_Figures current as of 2026-08-09. Pricing and moderation behaviour drift; re-check before relying on them._

Fallback reference. **If the interface or MCP tool you are calling reports its own limits, those
win.** A client may enforce a stricter prompt ceiling or duration set than the provider does. Never
raise a runtime limit to match this file, and never renumber a binding the interface gave you.

---

## Output

| Spec | Value |
|---|---|
| Duration | 4–15 seconds; the interface takes whole seconds |
| Frame rate | 24 fps |
| Audio | Native stereo, generated in the same pass as the picture |
| Resolutions | 768p · 2K · 4K |
| Aspect ratios | 21:9, 16:9, 4:3, 1:1, 3:4, 9:16 (plus Auto on reference modes) |
| Prompt ceiling | 7,000 characters |
| Languages | 11 stable: Arabic, Chinese, English, French, German, Italian, Japanese, Korean, Portuguese, Russian, Spanish |

**Measured, not quoted.** The official demo files were inspected directly: 24 fps and **32,000 Hz
stereo AAC** on every clip, at 2560×1440 (16:9), 1440×2560 (9:16) or 2944×1248 (21:9). "2K" means a
1440px short edge from 16:9 through 9:16; the 21:9 output holds roughly the same pixel budget
(2944×1248 ≈ 3.67 MP vs 3.69 MP) by trading height for width, so its short edge is smaller.

**Multi-shot is native.** A single generation can contain real cuts. You do not need to stitch
separate clips to get coverage — that is what the timed shot list is for.

---

## Cost

| Resolution | Per second | 5s | 15s |
|---|---|---|---|
| 768p | $0.08 | $0.40 | $1.20 |
| **2K** | **$0.13** | $0.65 | $1.95 |
| 4K | $0.16 | $0.80 | $2.40 |

Two things worth telling users:

- Some resellers quote roughly double the direct rate. The direct API and fal both publish these.
- **A silently filtered generation still bills in full.** Validation is not pedantry, it is money.

---

## Input limits

### Reference mode (Omni)

| Input | Limit |
|---|---|
| Images | up to 9 |
| Videos | up to 3, each 2–15s, 15s total |
| Audio | up to 3, each 2–15s, 15s total |
| Mixed total | 12 files |

Audio cannot be used alone — pair it with at least one image or video.

### First/last frame

Accepts 0, 1 or 2 images. Dimensions 256–5760px, aspect ratio between 5:2 and 2:5. With no image
it behaves as text-to-video. Output aspect follows the uploaded image.

### Formats

| Type | Formats | Max size |
|---|---|---|
| Image | JPG, JPEG, PNG, WEBP, HEIC, HEIF | 30 MB |
| Video | H.264/AVC or H.265/HEVC, AAC or MP3 audio | 50 MB |
| Audio | WAV, MP3 | 15 MB |

API request body caps at 64 MB — prefer URL-based media over inline uploads.

---

## Token syntax

MiniMax's own published examples use spaced labels numbered in **attachment order across all
types** — a video attached first, an image second and audio third become:

```text
Video 1   Image 2   Audio 3
```

Compact per-type tokens (`Image1`, `Video1`, `Audio1`) also bind correctly and appear in community
guides. What matters is that the number matches the attachment position the interface shows you.
Never use an `@` prefix. If the interface displays a different exact token, use theirs. Never infer
a token from a filename, a UUID or a library label.

Roles each reference type can credibly carry:

- **Image** — identity, wardrobe, prop appearance, product geometry, location, layout, material,
  lighting, graphic style
- **Video** — how a body moves, how a performance is paced, the order expressions arrive in, what
  the camera does, how it frames, how it cuts, and the room it was shot in
- **Audio** — the grain of a voice, its accent and rhythm, how a line is delivered, singing, a piece
  of music, or a single effect

Keep each reference inside its lane. The only time one asset should drive several categories at
once is when the user actually wants that entire asset recreated.

---

## Where it runs

| Route | Best for | Note |
|---|---|---|
| Hailuo AI | Trying it, no code | MiniMax's own app, full feature set |
| MiniMax API | Volume | Model ID `MiniMax-H3`, direct rates, pay-as-you-go |
| fal.ai | Developers | Three endpoints: text-to-video, image-to-video, reference-to-video |
| Open weights | Self-hosting | 33B on Hugging Face; caps at 768p |

The community licence excludes local deployment in the US, EU, UK and South Korea, requires visible
"MiniMax H3" attribution in products built on it, and needs separate written authorisation for
companies above $20M annual revenue. None of that applies to using the hosted API.

### Which route runs which shot type

| Shot type | Where it runs |
|---|---|
| 0 Text only | Hailuo text-to-video · fal `text-to-video` |
| 1 Reference scene · 2 Motion · 3 Voice · 7 Product | Hailuo **Omni Reference** · fal `reference-to-video` |
| 4 First/last frame | Hailuo **First/Last Frame** · fal `image-to-video` |
| 5 Targeted edit · 6 Environment/VFX | Hailuo Omni Reference with the source clip as a video reference |

There is no separate "edit" endpoint. An edit is a reference-to-video generation where the source
clip is the master reference — which is why it costs full rate and produces a new file.

---

## Architecture, briefly

33B dense transformer. Text encoder is Qwen3-VL-32B. The visual VAE compresses 16× spatially and
4× temporally; a separate stereo audio VAE runs alongside it.

The part that matters for prompting: 2K is produced by **regenerating** the output while re-reading
your original context, not by upscaling it afterwards. That is why small on-screen text and brand
marks survive at 2K when other models smear them — and why a specific, well-ordered prompt pays off
more here than on models that upscale blindly.
