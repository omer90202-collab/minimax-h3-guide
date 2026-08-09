# H3 Prompt Architect

**A skill that writes MiniMax H3 prompts for you.**

H3 gives you more control over identity, motion and sound than anything at its price — and punishes
a lazy prompt harder. Upload three reference images without telling it who does what and it averages
them: faces drift, jackets change colour, a stranger appears in the background. Each mistake costs
about $2 to discover.

This skill turns a sentence into a structured, paste-ready H3 prompt that gets it right far more
often on the first try. It knows the model's section order, the reference-token syntax, the timing
budget, and the specific line to add when something comes out wrong.

---

## What it does

You say:

> Two friends arguing on a rooftop at sunrise. Here are photos of both of them and a clip with
> the camera move I want.

You get back a complete prompt — every reference assigned one job and one exclusion, identities
locked, geography set, beats timed, sound specified, and the failure modes pre-empted:

```text
[REFERENCE USE]
Image 1 defines Maya's face, hair and wardrobe only. Do not inherit its background or pose.
Image 2 defines Daniel's face, hair and wardrobe only. Do not inherit its background or pose.
Video 3 defines camera movement and pacing only. Reject its actors, clothing and location.

[IDENTITY LOCKS]
Exactly two people. Preserve each face and outfit from first frame to last. No swaps.
...
```

Plus what the generation will cost before you press the button.

---

## Install

### Claude Code

Drop the `h3-prompt-architect` folder into your skills directory:

```bash
mkdir -p ~/.claude/skills
cp -R h3-prompt-architect ~/.claude/skills/
```

Restart Claude Code. That's it — it activates on its own whenever you mention H3, Hailuo, or
reference-based video prompting. You can also call it directly with `/h3-prompt-architect`.

### Claude Desktop / claude.ai

Settings → Capabilities → Skills → upload `h3-prompt-architect.zip`.

### Codex, Cursor, or any agent that reads project files

Copy the folder into your project root. Most agents pick up `SKILL.md` automatically. If yours
doesn't, paste the contents of `SKILL.md` into your system prompt or rules file — it's written to
work standalone.

### No coding tools at all?

Open `SKILL.md`, copy the whole thing, and paste it into a new chat with any capable assistant,
followed by your idea. You lose the automatic triggering, and nothing else.

---

## How to use it

Just describe what you want. The more you tell it, the less it has to assume:

- **What is uploaded, in order.** "Image 1 is her, image 2 is him, video 1 is the camera move."
- **What is actually said**, word for word, if anyone speaks.
- **How long** the clip should be — H3 does 4 to 15 seconds.

Then ask for the prompt. If a generation comes back wrong, tell the skill what went wrong instead
of re-rolling — it will name the cause and hand you the one line that fixes it. Re-rolling costs
another $2 and usually produces a different wrong result.

---

## What's inside

| File | What it's for |
|---|---|
| `SKILL.md` | The method — the reference map, section order, timing, validation |
| `references/blueprints.md` | A filled-in pattern for each of the eight shot types |
| `references/h3-spec.md` | Limits, formats, pricing, token syntax, which route runs what |
| `references/troubleshooting.md` | Symptom → cause → the exact line to add |
| `agents/openai.yaml` | Display metadata for Codex-style agent runners |
| `LICENSE` | MIT |

---

## The one idea behind all of it

> **Every reference gets exactly one job, and one explicit thing it must not do.**

You are casting a crew, not uploading a mood board. A reference with no job description will
volunteer for every job on set. That second line — the exclusion — is what most people skip, and
it's the line that actually stops the leakage.

Everything else in this skill is machinery around that one idea.

---

## Notes

Built to accompany the [MiniMax H3 Field Guide](https://omer90202-collab.github.io/minimax-h3-guide/).

Prompting technique here is derived from MiniMax's published documentation and prompt examples,
community guides, and hands-on use. The bracketed-section approach to H3 prompting is prior art —
it appears in several community resources, notably PromptSama's `minimax-h3-reference-prompts`;
this package is an independent write-up of the same underlying technique, with its own blueprints,
troubleshooting and cost guidance.

Resolution, frame rate and audio format were measured directly from MiniMax's official demo files.
Limits, pricing and licence terms come from MiniMax's and fal's published documentation.

Free to use and share. Not affiliated with MiniMax or Hailuo AI. The model's behaviour changes as
it's updated — if something here stops matching reality, trust the model.

MIT licensed.
