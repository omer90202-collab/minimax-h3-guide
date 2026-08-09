# Blueprints

One pattern per shot type. Mix them freely — most real briefs are hybrids. Replace every `<placeholder>` with a fact from the user's brief —
never leave a placeholder in a returned prompt. Trim any section the shot does not need.

---

## 1. Two-character reference scene

The workhorse. Use when images define who the people are and where they are.

```text
[REFERENCE USE]
Image1 defines <A>'s face, hair, body and wardrobe only. Do not inherit its background or pose.
Image2 defines <B>'s face, hair, body and wardrobe only. Do not inherit its background or pose.
Image3 defines the location, layout, materials and lighting only. Do not inherit any people in it.

[IDENTITY LOCKS]
Exactly two people appear in this clip. Preserve each face, hairline, body proportion and outfit
from first frame to last. <A> keeps <A>'s voice, <B> keeps <B>'s voice. No swaps of any kind.

[SCENE]
<Location, time of day>. <A> wants <objective>. <B> resists because <reason>.
The scene turns from <opening emotion> to <closing emotion>.

[DIALOGUE]
<A> says: "<exact line>"
<B> replies: "<exact line>"

[SCREEN GEOGRAPHY]
<A> stands frame-left, <B> frame-right. Hold that axis and keep eyelines matched throughout.

[SHOT LIST]
Pick ONE of the two forms below and delete the other. Scale the timings to the requested duration.

--- multi-shot (H3 cuts natively; each cut needs a motivation) ---
0–5s — <framing>; <camera>; <A> <action> and delivers the first line; cut motivated by <reason>.
5–10s — tighter <framing>; <B> <reaction/escalation> and the second line; cut motivated by <reason>.
10–15s — <framing>; <resolution action>; camera settles; hold <final tableau> with a sound tail.

--- single take (no cuts; framing changes come only from camera movement) ---
One continuous take. Every change of framing comes from the camera move, never from an edit.
0–5s — <opening framing>; <camera behaviour begins>; <action and first line>; end on <visible state>.
5–10s — the move has tightened to <framing>; <reaction and second line>; end on <visible state>.
10–15s — the move arrives at <framing>; <resolution>; camera settles; hold with a sound tail.

[ACTING]
<Posture, breath, where each character looks, what they do while listening, how attention shifts.>

[LIGHT AND IMAGE]
<Key light direction and quality, palette, contrast, lens character, depth of field.>

[CAMERA]
<Permitted moves and sizes. Which transitions are allowed.>

[PRODUCTION SOUND]
Native stereo <ambience>. Dialogue stays clearly above the bed. Include <motivated effects>.
<Music: none / a specific cue.>

[NEGATIVES]
No third person, no face drift, no wardrobe change, no voice swap, no broken eyelines,
no duplicate props, no subtitles, no unmotivated cuts.
```

---

## 2. Motion and camera transfer

Use when a clip has movement or camera work you want, on different people.

The whole job is separating what you're taking from what you're rejecting. Be blunt about it.

```text
[REFERENCE USE]
Image1 defines <A>'s identity and wardrobe only.
Video1 defines body motion, gesture timing, interaction rhythm and camera movement only.
Reject everything else from Video1: its actors, faces, clothing, location and lighting.

[TRANSFER]
Reproduce the full action order and camera behaviour of Video1 performed by <A> in <target scene>.
Preserve every pause, direction change, contact, handoff, occlusion and final position.

[CONTINUITY]
Exactly <n> people. Hold identity, wardrobe, screen direction and left/right geography.
Weight, contact, hair movement, fabric and shadow must read as physically real in the new scene.

[PRODUCTION SOUND]
Native stereo ambience for <target scene> with synchronised action effects. <Dialogue/music rule.>

[NEGATIVES]
No actor leakage from Video1, no identity swap, no missing beats, no added cuts, no mirrored
screen direction.
```

**Two things to check on the reference clip before spending:**

*Length.* If Video1's move is much shorter than your target duration, H3 stretches it — a 4-second
push-in spread across 12 seconds slows roughly 3×. Usually fine for a push-in, sludgy for anything
faster. If the lengths are far apart, either match the clip length or say so explicitly:
`Spread Video1's move evenly across the full <n> seconds.`

*Cuts.* Video1's edit is part of its motion. If it contains a cut and your prompt also says
"no cuts", you have written a contradiction and the result will look strange for no obvious reason.
Trim the reference to one continuous move, or take the edit rhythm deliberately:
`Video1 also defines the cut rhythm — reproduce its <n> cuts at the same beats.`

---

## 3. Voice transfer with exact dialogue

Use when audio supplies a voice and you need specific words in that voice, lip-synced.

Keep four things separate or they contaminate each other: who speaks, what is said, which
reference supplies the voice, and how the mix behaves.

```text
[REFERENCE USE]
Video1 defines the character on screen, the performance, the scene, the camera and the duration.
Audio1 defines only <A>'s voice timbre, accent, cadence and emotional delivery.
Do not inherit Audio1's words, background noise or music.

[DIALOGUE]
<A> says exactly, in <language and accent>: "<exact line>"
Delivery: <pace, volume, breath, restraint, where the emphasis lands>.

[SYNC AND MIX]
Lip sync must match the spoken line precisely. Keep the same face and identity throughout.
Preserve native stereo room tone under clear dialogue.

[NEGATIVES]
No added speech, no subtitles, no voice drift mid-line, no background music, no identity change.
```

---

## 4. First and last frame

Use when two images are the exact boundaries and H3 fills the middle.

```text
[BOUNDARY FRAMES]
Image1 is the exact first frame. Hold its subject count, identity, wardrobe, composition,
lighting and object positions at the start.
Image2 is the exact last frame. By the end, the shot should have travelled naturally into that
composition, that light and those object positions.

[ACTION]
In one continuous causal motion, <the smallest plausible action connecting the two frames>.
The same subjects and objects persist throughout — nothing enters or leaves.

[CAMERA AND SOUND]
<Single camera behaviour.> Native stereo <ambience and motivated effects>.

[NEGATIVES]
No cut, no teleporting, no duplicate subject, no identity change, no prop swap,
no discontinuous lighting.
```

---

## 5. Targeted edit

Use when one clip is the truth and you are changing exactly one thing in it.

The `[PRESERVE]` block is the important one. Without it H3 treats the whole clip as negotiable.

```text
[SOURCE MASTER]
Treat Video1 as the only source of truth — its people, their performance, the setting, the running
order of events, the camera, what overlaps what, the dialogue and the room tone all stand as-is.

[REFERENCE USE]
Image1 defines only <replacement>'s appearance, structure, material and colour.
Do not inherit its background, scale or pose.

[EDIT]
Swap <target> for <replacement>, and only that. One instance, never two.
Wherever the original travelled, touched, passed behind something, turned, grew, sped up or
dropped out of frame, the replacement does the same.

[PRESERVE]
Change nothing else: no other subject, object, action, background, light, camera move, cut,
line of dialogue, sound effect, ambience or the duration.
```

---

## 6. Environment or VFX replacement

Use to keep the performance and swap the world around it.

```text
[SOURCE MASTER]
Video1 defines foreground subjects, performance, motion, timing, camera and duration.

[REFERENCE USE]
Image1 defines only the target environment, atmosphere and lighting style.

[EDIT]
Replace <bounded background> with the target environment. Parallax, background motion, shadows,
reflections and transmitted light must respond correctly to the foreground performance and camera.

[PRESERVE]
Identity, hair and fabric edges, foreground objects, gesture timing, scale, occlusions, camera
path and duration all unchanged.

[NEGATIVES]
No edge halos, no colour spill, no altered performance, no changes outside the replaced region.
```

---

## 7. Product / hands-only commercial shot

The most common commercial case: a product reference, maybe a body-part reference, one subject,
no speech. Use blueprint 1's spine but swap the human-shaped sections for these.

```text
[REFERENCE USE]
Image1 defines the product only — exact geometry, proportions, finish, label artwork, typography
and label placement. Do not inherit its background, surface, scale, lighting or camera angle.
Image2 defines the hands only — skin tone, hand and finger shape, nails, jewellery.
Do not inherit its background, lighting, framing or pose.

[IDENTITY LOCKS]
Only hands and forearms appear — no face, no torso, no second person, no reflected figure.
Exactly one product exists in the clip; never a duplicate in frame or in the background.
Geometry and label stay identical from first frame to last, correctly oriented and unwarped
whenever the label faces camera.

[SCREEN GEOGRAPHY]
<Frame shape and camera angle>. <Where the product sits, where negative space sits, which edge
the hands enter from, where the product finishes.>

[SHOT LIST]
One continuous take, no cuts. Framing changes come only from the camera move.
<first half> — <opening framing>; <camera begins>; <the handling action>; end on <visible state>.
<second half> — <arrived framing>; <the reveal — label turned to camera and held>; hands withdraw;
camera settles and holds on the product alone with room tone continuing.

[ACTING]
Hands are deliberate and supported — contact before grip, no regripping, no tremor, a fractional
pause between movements, release a half-second after the product lands.

[NEGATIVES]
No face, no torso, no extra hands, no duplicate product. No label drift, no mirrored, warped or
invented text, no text beyond what the product carries. No background or lighting leaking from the
references. No cuts, no shake. No music, no dialogue, no captions or watermarks.
```

**Judge the label at 2K, never at 768p.** Small type only resolves on the regeneration pass, so a
cheap structure test can confirm staging but tells you nothing about legibility.

---

## Writing notes that apply to all seven

**Direct behaviour, not feelings.** "Anxious" gives H3 nothing to render. "Keeps glancing at the
door, turns the ring on her finger, starts the sentence twice" gives it everything.

**Motivate every camera move.** A move that follows an action reads as cinema. A move with no cause
reads as a drone demo.

**Sound is half the model.** H3 generates audio in the same pass, so ambience, footsteps, fabric and
room tone cost nothing extra and are the fastest way to make a clip feel real. Tie every sound to
something visible. "Cinematic audio" is not a sound.

**Negatives should be observable.** "No extra people", "no wardrobe change", "no subtitles" are
checkable. "High quality", "not weird", "professional" are not — they consume characters and change
nothing.
