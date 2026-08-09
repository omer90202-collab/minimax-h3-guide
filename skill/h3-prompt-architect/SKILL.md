---
name: h3-prompt-architect
description: Build, rewrite, or fix prompts for the MiniMax H3 video model. Use whenever the user wants an H3 prompt, is uploading reference images/video/audio to H3 or Hailuo, wants character or voice consistency across shots, wants to transfer motion or camera moves from one clip to another, wants exact spoken dialogue with lip-sync, is editing an existing clip, or is getting drifting faces, swapped clothing, extra people, ignored references or wrong dialogue out of H3. Also use for first/last-frame transitions and for costing a generation before spending on it.
---

# H3 Prompt Architect

Turn a rough idea into one paste-ready MiniMax H3 prompt that lands on the first try.

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
Image1 defines Maya's face, hair and wardrobe only.
Do not inherit its background, pose, framing or lighting.
```

That second line is what most people skip, and it is the line that actually stops the leakage.

---

## Workflow

### Step 1 — Identify the shot type

Pick the smallest one that fits. This determines the whole prompt shape.

| Type | You have | Give it |
|---|---|---|
| **Text only** | Nothing uploaded | Scene + shot list |
| **First/last frame** | 1–2 images as boundary frames | The single motion that connects them |
| **Reference scene** | Images/video/audio defining the result | A full authority map |
| **Motion transfer** | A clip whose movement or camera you want | Explicit "take this, reject that" |
| **Voice transfer** | Audio of a voice you want | One speaker, one exact line |
| **Targeted edit** | A source clip to change one thing in | A master + a preserve list |

**Hybrids are normal — most real briefs are one.** "Photos of my two characters, plus a clip whose
camera move I want" is a reference scene *and* a motion transfer. Combine them: take the base
structure from the dominant type, then fold in the other type's key clause. When a reference
supplies camera or motion, its rejection line (`Reject its actors, clothing, location…`) must
travel with it into whatever blueprint you're building, and **the `[CAMERA]` section must not then
issue competing instructions** — one owner for camera, always.

**Naming.** Every spoken line needs an owner named in the same sentence. If the brief gives no
names, a role label is a perfectly good owner — `THE BARISTA says:` works as well as `MAYA says:`.
Invent a proper name only if it makes the prompt read more naturally, and say so in your watch-outs.
Never write "someone says", "they say", or rely on "respectively".

**Also decide these two, every time** — both are required fields in every H3 interface:

- **Resolution.** Default to 2K. The 2K pass regenerates against your context rather than upscaling,
  which is what holds faces and on-screen text together. 768p only for cheap structure tests (see Step 5).
- **Aspect ratio.** Match it to the staging. A two-shot across a counter dies in 9:16; a single
  talking subject survives anywhere. Decide before you write the shot list, not after.

Fill in obvious production details yourself. Only ask the user when the answer would change
**who owns an identity, which clip is the master, or what is actually said out loud** — getting
those wrong wastes a paid generation.

When you are producing a prompt rather than having a conversation, don't stop to ask: **commit to
the reading the references actually support, and put the alternative in your watch-outs with the
exact edit needed to switch.** A user with hand references and no face reference gets a hands-only
shot — plus one line telling them what to upload if they wanted her in frame.

### Step 2 — Cast every reference

Before writing a single line of prompt, build this table internally:

| Token | Who/what it is | It controls | It must NOT bring |
|---|---|---|---|
| `Image1` | Maya | face, hair, wardrobe | background, pose, lighting |
| `Video1` | reference clip | camera move, pacing | its actors, its location |
| `Audio1` | voice | timbre, accent, delivery | its background noise |

**Token syntax is strict:** `Image1`, `Image2`, `Video1`, `Audio1`. No `@`, no space before the
number, numbered in upload order. If the interface shows you a different label, use its label
exactly. Never renumber, and never write "respectively" — bind each character by name.

Read `references/blueprints.md` for the ready-made pattern for each shot type.

### Step 3 — Write it in this order

Stable facts first, then action, then finishing. Use only the sections you actually need —
an unused empty section is noise, and noise costs quality.

```text
[REFERENCE USE]          who each reference is and what it must not bring
[IDENTITY LOCKS]         subject count, faces, wardrobe, prop and voice ownership
[SCENE]                  location, what each character wants, the emotional turn
[DIALOGUE]               exact quoted lines, one speaker each
[SCREEN GEOGRAPHY]       who is frame-left, who is frame-right, eyelines
[SHOT LIST]              timed beats
[ACTING]                 observable behaviour, not emotion labels
[LIGHT AND IMAGE]        lighting, palette, lens, depth
[CAMERA]                 permitted moves and transitions
[PRODUCTION SOUND]       ambience, effects, dialogue clarity, music policy
[NEGATIVES]              the specific failures you are guarding against
```

**Why this order works:** H3 resolves the context as a whole. Facts stated before the action get
treated as constraints on the action. State them after, and they read as suggestions.

Section names vary by shot type — a transfer uses `[TRANSFER]` and `[CONTINUITY]`, an edit uses
`[SOURCE MASTER]`, `[EDIT]` and `[PRESERVE]`. The order above is the reference-scene spine; each
blueprint shows its own. What never changes is the principle: stable truth, then action, then finish.

**Watch the character count.** A fully-populated two-hander runs 6,000–6,800 characters against a
7,000 ceiling. If you go over, cut in this order: `[LIGHT AND IMAGE]` detail first, then `[ACTING]`,
then trim `[NEGATIVES]` to the failures that are actually plausible for this shot. Never cut
`[REFERENCE USE]`, `[IDENTITY LOCKS]` or `[DIALOGUE]` — those are what stop the expensive failures.

### Step 4 — Time the beats

For anything with more than one thing happening, use consecutive non-overlapping ranges inside the
clip length. A reliable three-act shape, **in proportions so it scales to any duration**:

```text
first third   establish — who, where, the relationship, the first line
middle third  escalate  — closer framing, the interruption, the reaction
final third   resolve   — the answer, the decisive action, camera settles, sound tail
```

At 15s that's 0–5 / 5–10 / 10–15. At 12s it's 0–4 / 4–8 / 8–12. At 8s, use two beats, not three.
Always write real numbers that add up to the requested duration — never leave a blueprint's
example timings in place.

Each range gets **one primary event** and a visible end state:

```text
<time> — <framing>; <camera behaviour>; <what happens>; <speaker: "exact line">; <end state>.
```

Budget roughly **one sentence of dialogue per 5 seconds**. Cramming three lines into four seconds
produces rushed, clipped speech. Leave room for silence and reactions — they are what makes a clip
feel directed rather than generated.

For a single continuous action, skip timestamps and write natural prose. Timestamps on a simple
shot over-constrain it.

### Step 5 — Check before you spend

**Every generation costs real money** — roughly $0.13 per second at 2K, so about $2 for a 15-second
clip. A prompt that fails validation costs the same as one that succeeds. Run this list first:

- [ ] Every reference has one job **and** an exclusion line
- [ ] Every identity, voice and prop has exactly one owner
- [ ] Tokens match upload order and are used consistently throughout
- [ ] Subject count is stated explicitly (this is the #1 cause of a mystery third person)
- [ ] Dialogue fits the time budget, one speaker per line, exact wording
- [ ] Time ranges are consecutive and non-overlapping
- [ ] Camera direction does not fight the motion a reference is supplying
- [ ] Negatives name observable failures, not vague quality words
- [ ] Resolution and aspect ratio are decided and stated
- [ ] Prompt is under 7,000 characters
- [ ] Nothing is invented about a reference you cannot actually see

If a reference was described to you but not shown, say so in your assumptions rather than
inventing its contents.

### Recommend a cheap structure pass first

For anything ambitious — multiple references, motion transfer, dialogue, more than two people —
tell the user to **run it once at 768p before committing to 2K.**

At $0.08/s versus $0.13/s, a 12-second test costs $0.96 instead of $1.56. You are not judging
beauty at that stage, you are checking structure: is it two people and not three, is the geography
right, does the camera behave, do the lines land on the right mouths. Then run the *identical*
prompt at 2K.

Two passes this way costs about $2.50. Two blind 2K attempts cost $3.12 and are likelier to leave
you with nothing usable.

**When the rules bracket a shot on both sides — simple staging but two or more references — run the
test.** References are the thing that surprises you; a simple shot with clean references is the only
case worth skipping it for.

**One limit to say out loud:** 768p cannot show you whether small text survives. Labels, signage and
titles only resolve on the 2K regeneration pass. So for product and title work, use the cheap pass
to confirm staging — one bottle, right hands, no face, camera behaving — and tell the user plainly
that legibility gets judged on the 2K run and nowhere else.

---

## What H3 will and won't render

Worth knowing before you write, so you don't burn a generation finding out:

- **Real brands and products** render accurately on request — genuinely unusual, and the reason H3
  is viable for commercial product work.
- **Real public figures** generate cleanly, including a matching voice and lip-sync.
- **Tension, weapons and dramatic stakes** clear when they serve the scene.
- **Studio-owned characters** are refused. Build original characters instead of borrowing famous ones.
- **Graphic gore** is quietly removed while the generation still succeeds and still bills. If gore
  is essential to the shot, expect to pay for a clip that arrives without it.

Remind the user that a permissive model is not permission — their own use is still governed by
MiniMax's terms and by local law on likeness and disclosure.

---

## Output

Return, in this order:

1. **Reference map** — a compact table, only when two or more references are in play.
2. **The prompt** — one plain-text code block, complete and paste-ready. No commentary inside it.
3. **Cost** — clip length × rate, so they know what pressing the button costs.
4. **Watch-outs** — only if something genuinely needs flagging.

Do not offer competing variants unless asked; pick the strongest one and commit. Never submit a
generation on the user's behalf unless they explicitly ask you to.

If the user reports a bad result, do not simply regenerate — read
`references/troubleshooting.md`, name the likely cause, and fix that specific line.

---

## Reference files

- `references/blueprints.md` — a filled-in pattern for each of the six shot types
- `references/h3-spec.md` — verified capability limits, formats, and what the numbers actually mean
- `references/troubleshooting.md` — symptom → cause → the exact line to add
