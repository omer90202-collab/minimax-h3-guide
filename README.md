# MiniMax H3 — The Complete Field Guide

An independent, bilingual (English / Hebrew RTL) guide to **MiniMax H3** — the omni-modal video
model MiniMax released on 31 July 2026, with open weights following on 3 August 2026.

**Live site → https://omer90202-collab.github.io/minimax-h3-guide/**

> **Unofficial.** This project is not published, endorsed, reviewed or sponsored by MiniMax or
> Hailuo AI. All product names and trademarks belong to their respective owners.

---

## What's covered

| Section | Contents |
|---|---|
| Multimodal context | The one-prompt / three-modalities demo, with the original inputs and MiniMax's verbatim prompt |
| Capabilities | Omni-reference, native audio, multi-shot, instruction editing, text & brand rendering, V2V motion, in-context 2K, aspect ratios |
| Specs | Duration, frame rate, resolution, audio, languages, reference limits, architecture, VAE, API model ID |
| Demos | Eight official demo clips with source resolutions and durations |
| Benchmarks | Artificial Analysis Elo scores for video editing, text-to-video and image-to-video |
| Pricing | Per-second rates at 768p / 2K / 4K, plus reseller and billing gotchas |
| Limits | What clears moderation, what is hard-blocked, and what is silently stripped |
| Prompting | The seven-part prompt formula, structured fields, reference and voice syntax, a worked example |

## Verified rather than repeated

Two claims here come from direct testing, not from a secondary source:

- **Real public figures generate cleanly**, including a matching voice and accurate lip-sync. The
  hard block is on studio-owned *characters* — a copyright line, not a likeness line. Disney,
  Universal and Warner Bros. Discovery sued MiniMax over 30+ works, which is what that filter
  is defending against.
- **The audio spec is real.** Every H3 demo file was inspected directly with `ffmpeg`: all are
  24 fps with 32 kHz stereo AAC, at 2560×1440 (16:9), 2944×1248 (21:9) or 1440×2560 (9:16).

## Tech

Two static HTML files. No build step, no framework, no back end, no database, no tracking.

- **GSAP + ScrollTrigger** — scroll-driven scenes
- **Lenis** — smooth scrolling
- Scroll scenes adapted from the Animmaster Awwwards component pack
- Bilingual via `data-en` / `data-he` attributes swapped at the leaf level, so switching language
  never breaks an animation timeline
- All CDN dependencies pinned with Subresource Integrity hashes
- Content Security Policy restricts every load to an explicit allowlist

### Running locally

```bash
npx -y http-server . -p 8123 -c-1
```

Then open http://localhost:8123.

## Accessibility

Targets WCAG 2.1 AA (and IS 5568 in Israel): full keyboard navigation, visible focus, skip link,
AA contrast with an automatic high-contrast palette, complete `prefers-reduced-motion` support,
labelled media, and correct language/direction switching. Details and known limitations are in
[`legal.html#a11y`](legal.html).

## Licence and attribution

- **Original text, design and code** — MIT, see [LICENSE](LICENSE).
- **Demo videos, reference image and reference audio** — © MiniMax. Published on their
  [official announcement page](https://www.minimax.io/blog/minimax-h3) and reproduced here for
  commentary, criticism and review. Re-encoded to a lower resolution and bitrate for web
  delivery; the originals are 2K. No ownership is claimed and no affiliation is implied.

Full source-by-source attribution is in [`legal.html#credits`](legal.html).

### Takedown

If you own material used here and want it removed, **open an issue and it will be taken down
promptly — no argument required.** Corrections are equally welcome and will be fixed and dated.

## Disclaimer

Information only, provided as-is without warranty. Not legal, financial or technical advice.
Pricing, rankings, licence terms and moderation behaviour change frequently — verify against
MiniMax's own documentation before making a commercial decision.

This guide documents what the model *does*, not what you are permitted to do with it. Generating
a recognisable real person can engage right-of-publicity, defamation, election-advertising and
synthetic-media disclosure rules that vary by country. A permissive model is not permission.
