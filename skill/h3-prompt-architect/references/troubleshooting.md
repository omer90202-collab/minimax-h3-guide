# Troubleshooting

When a generation comes back wrong, the instinct is to re-roll. Don't — you pay again and usually
get a different wrong result. Almost every H3 failure traces to a missing constraint, and the fix
is one line.

Find the symptom, add the line, regenerate once.

---

## Identity and people

**A face changes partway through the clip**
The identity reference was given a job but no duration. Lock it across time.
```text
Preserve <A>'s face, hairline and body proportions identically from the first frame to the last.
```

**An extra person appears**
Subject count was never stated. H3 infers population from context, and crowded references imply
crowds. This is the single most common H3 failure.
```text
Exactly two people appear in this clip. No background figures, no reflections of other people.
```

**Two characters swap faces or clothes**
Ownership was implied by order rather than by name. Never rely on "respectively".
```text
Image 1 is Maya only. Image 2 is Daniel only. Maya keeps Maya's face and jacket throughout;
Daniel keeps Daniel's. No swaps.
```

**A face from the reference clip bleeds into the result**
Motion transfer without an explicit rejection. Taking the movement does not imply refusing the actor.
```text
Video 1 supplies motion and camera only. Reject its actors, faces, clothing and location entirely.
```

---

## References being ignored

**H3 seems to blend all the references into an average**
Each reference is competing for every attribute. Give each one a boundary.
```text
Image 1 defines identity only. Image 3 defines the location only, and contributes no people.
```

**Background or lighting leaked in from a character photo**
A portrait carries a whole scene with it unless you say otherwise.
```text
Do not inherit Image 1's background, lighting, framing or pose.
```

**A reference was ignored completely**
Usually a token mismatch — the number in the prompt doesn't match the attachment position, or an
`@` crept in. Numbering follows attachment order across all types, so a video attached first is
`Video 1` even if an image is the first *image*. If the interface displays its own labels, use
those exactly.

---

## Dialogue and audio

**Speech is rushed or words get clipped**
Too much dialogue for the time budget. Roughly one sentence per five seconds.
Either cut the line or extend the range.

**Lips don't match the words**
The visual reference and the spoken line are competing. Say which wins.
```text
Lip sync must match the spoken line exactly, not the mouth movement in Video 1.
```

**The wrong character speaks**
Every line needs a named owner in the same sentence as the words.
```text
Maya says: "<line>"
```

Not `someone says`, not `they say`, not `a voice says` — those leave the line unowned.

**Reference audio's own words came through**
The audio was scoped as a whole clip instead of as a voice.
```text
Audio 1 supplies voice timbre, accent and cadence only. Do not inherit its words or background.
```

**Unwanted music appeared**
Music policy is not silent by default.
```text
No background music. Native stereo ambience only.
```

**The clip is silent when it shouldn't be**
Something in the prompt read as a request for no audio. Ask for the sound explicitly and tie each
element to something visible on screen.

---

## Camera and staging

**Characters flip sides between shots**
Screen geography was never declared.
```text
Maya holds frame-left, Daniel frame-right. Do not cross the axis. Keep eyelines matched.
```

**Unmotivated cuts appear**
H3 does native multi-shot, so it will cut on its own if the shot list is vague.
```text
One continuous take, no cuts.
```
Or specify what motivates each transition in the shot list.

**The camera fights the reference motion**
The `[CAMERA]` block contradicts a movement `Video 1` is supplying. Pick one owner for camera —
either the reference or your direction, never both.

---

## Text and products

**On-screen text is garbled**
Quote it exactly and say where it sits. Generate at 2K — the regeneration pass is what recovers
small type, and 768p will not hold it.
```text
The label reads exactly "MORNING BLEND", centred, in the same typeface throughout.
```

**A product's shape drifts between frames**
```text
Image 1 defines the bottle's exact geometry, proportions and label placement.
Preserve them unchanged through every frame.
```

---

## Things that are not bugs

**Gore was removed but the clip still generated and still billed.**
Working as designed. Graphic injury is stripped silently rather than refused. If it's essential to
the shot, H3 is the wrong tool.

**A named film or game character was refused outright.**
Studio-owned IP is hard-blocked. Build an original character with the same energy instead — describe
the silhouette, palette and attitude rather than naming the property.

**A real public figure generated instead of being refused.**
Expected — the hard block is on studio-owned characters, not on real humans. It is not a licence to
publish: see the acceptable-use note in `SKILL.md`.

---

## When nothing obvious is wrong

Cut the prompt in half. Over-specified prompts fail in ways that look random, because contradictions
hide in the volume. Get a clean simple version landing first, then add one constraint at a time —
you will usually find the offending line within two generations.
