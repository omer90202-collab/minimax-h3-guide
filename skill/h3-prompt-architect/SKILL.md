---
name: h3-prompt-architect
description: Build, rewrite, or fix prompts for the MiniMax H3 video model. Use whenever the user wants an H3 prompt, is uploading reference images/video/audio to H3 or Hailuo, wants character or voice consistency across shots, wants to transfer motion or camera moves from one clip to another, wants exact spoken dialogue with lip-sync, is editing an existing clip, or is getting drifting faces, swapped clothing, extra people, ignored references or wrong dialogue out of H3. Also use for first/last-frame transitions and for costing a generation before spending on it.
---

# H3 Prompt Architect

Turn a rough idea into one paste-ready MiniMax H3 prompt that gets it right far more often on the
first try.

H3 is not a "describe a pretty picture" model. It reads text, images, video and audio as a
**single merged context** and tries to satisfy all of it at once. That is its superpower and its
failure mode: give it three references without telling it who does what, and it averages them —
faces drift, jackets change colour, a second person appears.

Everything in this skill exists to prevent that.

---

## The one rule everything else serves

> **Every reference gets exactly one job, and one explicit thing it must NOT do.**

You are casting a crew, not uploading a mood board. A reference with no job description will
volunteer for every job on set.

```text
Image 1 defines Maya's face, hair and wardrobe only.
Do not inherit its background, pose, framing or lighting.
```

That second line is what most people skip, and it is the line that actually stops the leakage.

---

## Workflow

### Step 1 — Identify the shot type

Pick the smallest one that fits. This determines the whole prompt shape. Each maps to a numbered
blueprint in `references/blueprints.md`.

| # | Type | You have | Give it |
|---|---|---|---|
| 0 | **Text only** | Nothing uploaded | Scene, explicit subject count, shot list |
| 1 | **Reference scene** | Images/video/audio defining the result | A full reference map |
| 2 | **Motion transfer** | A clip whose movement or camera you want | Explicit "take this, reject that" |
| 3 | **Voice transfer** | Audio of a voice, *plus* an image or clip | One speaker, one exact line |
| 4 | **First/last frame** | 1 or 2 images as boundary frames | The single motion that connects them |
| 5 | **Targeted edit** | A source clip to change one thing in | A master + a preserve list |
| 6 | **Environment / VFX** | A clip to keep, a world to swap | Foreground preserved, background replaced |
| 7 | **Product / hands-only** | A product shot, maybe hands | Geometry and label locked, no face |

**Hybrids are normal — most real briefs are one.** "Photos of my two characters, plus a clip whose
camera move I want" is 1 + 2. Take the base structure from the dominant type, then fold in the
other's key clause. When a reference supplies camera or motion, its rejection line must travel with
it, and **the `[CAMERA]` section must not then issue competing instructions** — one owner for
camera, always.

**Naming.** Every spoken line needs an owner named in the same sentence. If the brief gives no
names, a role label works fine — `THE BARISTA says:` is as good as `MAYA says:`. Never write
"someone says", "they say", or rely on "respectively".

**Duration.** If the user doesn't name one, default to **10 seconds** and say so in one line.
Dialogue-heavy scenes want 12–15; a single gesture or product reveal is better at 6–8.
**Anything over 15 seconds is not one generation** — plan it as consecutive shots, each its own
generation, carrying identity forward by reusing the same reference images plus a still exported
from the previous clip's last frame. Tell the user this before writing anything; it changes
their budget.

**Resolution and aspect ratio.** Decide both wherever the interface offers them — reference and
text modes let you set both; First/Last Frame takes its aspect from the uploaded image. Default to
**2K**: the 2K pass regenerates against your context rather than upscaling, which is what holds
faces and on-screen text together. Use 768p only for structure tests (Step 5). 4K exists at a
higher rate but is not on every route — check before promising it. Match aspect to the staging: a
two-shot across a counter dies in 9:16.

Fill in obvious production details yourself. Only ask the user when the answer would change
**who owns an identity, which clip is the master, or what is actually said out loud**.

When you are producing a prompt rather than having a conversation, don't stop to ask: **commit to
the reading the references actually support, and put the alternative in your watch-outs with the
exact edit needed to switch.** A user with hand references and no face reference gets a hands-only
shot — plus one line telling them what to upload if they wanted her in frame.

### Step 2 — Check the inputs will even be accepted

Read `references/h3-spec.md` whenever the user has uploaded anything, or asks about cost, duration,
resolution or formats. The caps that bite first:

**9 images (≤30 MB each) · 3 videos (2–15s each, 15s total, ≤50 MB, H.264/HEVC) · 3 audio clips
(2–15s each, 15s total, ≤15 MB) · 12 files total · audio never alone — always pair it with an
image or a clip.**

If a reference is too long or too large, say so **before** writing a line of prompt. A user with a
30-second phone clip needs to know it must be trimmed, not discover it after they've written a brief.

### Step 3 — Build the reference map

Before writing any prompt, build this internally:

| Token | Who/what it is | It controls | It must NOT bring |
|---|---|---|---|
| `Image 1` | Maya | face, hair, wardrobe | background, pose, lighting |
| `Video 2` | reference clip | camera move, pacing | its actors, its location |
| `Audio 3` | voice | timbre, accent, delivery | its words, its background |

**Token syntax.** MiniMax's own published examples use spaced labels numbered in **attachment
order across all types** — a video attached first, an image second and audio third become
`Video 1`, `Image 2`, `Audio 3`. Compact per-type tokens (`Image1`, `Video1`) also bind correctly
and appear in community guides. What matters is that **the number matches the attachment position
the interface shows you**. Never use an `@` prefix, and never rely on "respectively" — bind each
character by name.

**Renumber the blueprints to match the actual attachment order.** The blueprints in
`references/blueprints.md` number per type for readability. If the user attached a video first and
an image second, that is `Video 1` and `Image 2` — not `Image 1` and `Video 1`. Follow the
interface, never the blueprint.

**Several images of one person.** Bind them as a group, not as rivals:

```text
Image 1, Image 2 and Image 3 are the same person, Maya — use them together as one identity across
angles. Do not treat them as different people, and do not inherit any of their backgrounds,
poses or lighting.
```

Then state how many distinct people exist. Three photos of one person is exactly how a phantom
third character gets born.

### Step 4 — Write it in this order

Stable facts first, then action, then finishing. Use only the sections your shot needs — an unused
section is noise, and noise costs quality.

```text
[REFERENCE USE]          what each file controls — and what it must not bring
[IDENTITY LOCKS]         subject count, faces, wardrobe, prop and voice ownership
[SCENE]                  location, what each character wants, the emotional turn
[DIALOGUE]               exact quoted lines, one named speaker each
[SCREEN GEOGRAPHY]       who is frame-left, who is frame-right, eyelines
[SHOT LIST]              timed beats
[ACTING]                 observable behaviour, not emotion labels
[LIGHT AND IMAGE]        lighting, palette, lens, depth
[CAMERA]                 permitted moves and transitions
[PRODUCTION SOUND]       ambience, effects, dialogue clarity, music policy
[NEGATIVES]              the specific failures you are guarding against
```

**Why this order works:** H3 resolves the context as a whole. Facts stated before the action get
treated as constraints on it. State them after, and they read as suggestions.

Section names vary by shot type — a transfer adds `[TRANSFER]` and `[CONTINUITY]`, an edit uses
`[SOURCE MASTER]`, `[EDIT]` and `[PRESERVE]`, voice work adds `[SYNC AND MIX]`, first/last frame
uses `[BOUNDARY FRAMES]` and `[ACTION]`. Each blueprint shows its own. What never changes is the
principle: stable truth, then action, then finish.

**Watch the character count, but don't chase it.** A fully-populated two-hander lands around
2,500–3,500 characters against a 7,000 ceiling, so you will rarely be near it — if you are, you are
probably padding. If you genuinely go over, cut `[LIGHT AND IMAGE]` detail first, then `[ACTING]`,
then trim `[NEGATIVES]` to the failures actually plausible for this shot. Never cut
`[REFERENCE USE]`, `[IDENTITY LOCKS]` or `[DIALOGUE]`.

### Step 5 — Time the beats

For anything with more than one thing happening, use consecutive ranges inside the clip length.
A reliable three-act shape, **in proportions so it scales to any duration**:

```text
first third   establish — who, where, the relationship, the first line
middle third  escalate  — closer framing, the interruption, the reaction
final third   resolve   — the answer, the decisive action, camera settles, sound tail
```

At 15s that's 0–5 / 5–10 / 10–15. At 12s it's 0–4 / 4–8 / 8–12. At 8s, use two beats, not three.
Always write real numbers that add up to the requested duration — never leave a blueprint's example
timings in place.

Each range gets **one primary event** and a visible end state:

```text
<time> — <framing>; <camera behaviour>; <what happens>; <speaker: "exact line">; <end state>.
```

Budget roughly **one sentence of dialogue per 5 seconds**. Cramming three lines into four seconds
produces rushed, clipped speech. Leave room for silence and reactions — they are what makes a clip
feel directed rather than generated.

For a single continuous action, skip timestamps and write natural prose. Timestamps on a simple
shot over-constrain it.

### Step 6 — Check before you spend

**Every generation costs real money** — about $0.13 per second at 2K, so roughly $2 for a
15-second clip. A prompt that fails validation costs the same as one that succeeds.

- [ ] Every reference has one job **and** an exclusion line
- [ ] Every identity, voice and prop has exactly one owner
- [ ] Tokens match the attachment order the interface displays
- [ ] Subject count is stated explicitly (the #1 cause of a mystery third person)
- [ ] Dialogue fits the time budget, one speaker per line, exact wording
- [ ] Time ranges run consecutively and no beat is described twice
- [ ] Camera direction does not fight the motion a reference is supplying
- [ ] Negatives name observable failures, not vague quality words
- [ ] Uploaded media is inside the size, length and format caps
- [ ] Resolution, aspect ratio and duration are decided
- [ ] Prompt is under 7,000 characters
- [ ] Nothing is invented about a reference you cannot actually see

If a reference was described to you but not shown, say so in your assumptions rather than
inventing its contents.

**Recommend a cheap structure pass.** For anything ambitious — multiple references, motion
transfer, dialogue, more than two people — tell the user to run it once at 768p before committing
to 2K. At $0.08/s versus $0.13/s, a 12-second test costs $0.96 instead of $1.56.

When the rules bracket a shot on both sides — simple staging but two or more references — run the
test. References are what surprises you.

**What the cheap pass does and does not carry.** If the interface exposes a seed, note the one your
768p test used and reuse it at 2K — otherwise the 2K run is an independent generation, not a bigger
version of the same shot. Without a seed, treat the test as a check on the *prompt's logic* — does
this wording produce two people and not three, does it produce the geography you asked for — rather
than as a preview of the shot you are about to buy.

**One limit to say out loud:** 768p cannot show you whether small text survives. Labels, signage and
titles only resolve on the 2K pass. For product and title work, use the cheap pass to confirm
staging and tell the user plainly that legibility gets judged at 2K and nowhere else.

---

## What H3 will and won't render

Worth knowing before you write, so the user doesn't burn a generation finding out:

- **Real brands and products** render accurately on request — genuinely unusual, and the reason H3
  is viable for commercial product work. Note that rendering a brand you don't own into commercial
  work is a trademark question, not a model question; the model's willingness is not a defence.
- **Real people.** The hard block is on studio-owned characters, not on real humans, and prompts
  naming real public figures have generated rather than being refused. Treat that as observed, not
  guaranteed — moderation behaviour changes without notice. It is also the fastest way to breach
  MiniMax's acceptable-use policy, which prohibits impersonation without consent and undisclosed
  synthetic media, on top of right-of-publicity and election-advertising law that varies by
  country. Don't build a workflow on it.
- **Tension, weapons and dramatic stakes** clear when they serve the scene.
- **Studio-owned characters** are refused. Build original characters instead of borrowing famous
  ones — describe the silhouette, palette and attitude rather than naming the property.
- **Graphic gore** is quietly removed while the generation still succeeds and still bills. If gore
  is essential, H3 is the wrong tool.

---

## Output

Return, in this order:

1. **Settings** — duration, resolution, aspect ratio and **which route/mode to use** as a one-line
   note **above** the prompt. These are interface fields; never write them inside the prompt.
   `references/h3-spec.md` maps each shot type to its route — an edit is a reference-to-video
   generation, not an "edit" button, and users will look for one.
2. **Reference map** — a compact table, only when two or more references are in play.
3. **The prompt** — one plain-text code block, complete and paste-ready. No commentary inside it.
4. **Cost** — clip length × rate at the direct API/fal rate ($0.08/s at 768p, $0.13/s at 2K),
   labelled as such. The Hailuo consumer app bills in plan credits, and resellers often charge
   about double, so say which rate you're quoting.
5. **Watch-outs** — only if something genuinely needs flagging.

Do not offer competing variants unless asked; pick the strongest and commit. Never submit a
generation on the user's behalf unless they explicitly ask.

If the user reports a bad result, do not simply regenerate — read
`references/troubleshooting.md`, name the likely cause, and fix that specific line.

---

## Reference files

- `references/blueprints.md` — a filled-in pattern for each of the eight shot types
- `references/h3-spec.md` — verified limits, formats, pricing, token syntax, routes
- `references/troubleshooting.md` — symptom → cause → the exact line to add
- `agents/openai.yaml` — display metadata for Codex-style agent runners
