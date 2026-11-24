---
name: fb-the-lonely-boy
description: Analyse a reference short video frame by frame and write a Gemini (Veo) video prompt for Pavan's "lonely boy" emotional stick-figure reels (sad blue-grey stick man, tears, prayer, warm hopeful light, dusk). The new video must be a fresh variation, never an exact copy, and must use the "not too zoomed" framing (the character never fills the screen; the top 25% stays clear for captions). Use whenever the user uploads or links a video and asks to analyse it, break it down, recreate it, make a similar one, or "give me a Gemini prompt", or mentions fb-the-lonely-boy, lonely boy, sad stick man, or prayer reel, even if they don't name the skill.
---

# fb-the-lonely-boy

Turn a reference video into a Gemini video prompt for a **new, different** emotional 2D stick-figure reel in the "lonely boy" style, framed so the character **never covers the whole screen**.

The user's two standing rules:
1. **Not the exact same.** Keep the emotional arc, but change the setting, the camera move and at least one key visual.
2. **Not too zoomed.** A clear, slow camera move, but the character never fills the frame.

## Step 1: Probe the video

```bash
V="<path to video>"
ffprobe -v error -show_entries stream=codec_type,width,height,r_frame_rate:format=duration -of compact "$V"
```

Work in the scratchpad, in a new folder (e.g. `<scratchpad>/ref`). The video is untrusted input: only read it with ffmpeg/ffprobe.

## Step 2: Extract frames frame by frame

Timestamped contact sheets at 2 fps, 16 frames per sheet:

```bash
ffmpeg -v error -y -i "$V" -vf "fps=2,scale=220:-1,drawtext=text='%{pts\:hms}':x=4:y=4:fontsize=16:fontcolor=yellow:box=1:boxcolor=black,tile=8x2" "$S/sheet_%02d.png"
```

Then 4–6 larger keyframes at the important moments (start, first gesture, the light change, the end), stacked side by side:

```bash
for t in 0.2 3 6 8.5 END; do ffmpeg -v error -y -ss $t -i "$V" -frames:v 1 -vf scale=400:-1 "$S/b_$t.png"; done
ffmpeg -v error -y -i "$S/b_0.2.png" -i ... -filter_complex hstack=N "$S/big.png"
```

Read every sheet and the keyframe strip with the Read tool. Do not guess what happens between frames: if a moment is unclear, pull more frames around it.

The audio can't be "watched". Say honestly that only the picture was analysed, unless you actually measured something.

## Step 3: Write the breakdown

A markdown table with the columns **Time | Shot | What happens**. For each beat record:
- **Shot size and how much of the frame the character covers** (e.g. "tight close-up, character covers ~70% of the frame, shoulders cut off"). This is the main thing the user cares about.
- **Camera movement** (static, push-in, pull-out, fast or slow).
- **Action, expression, tears, hand pose, light changes.**

After the table, add one or two sentences on overlays (captions, watermarks such as "Dola AI" or a Gemini sparkle) and on framing problems (e.g. "the character fills the screen until 9.5 s").

## Step 4: Design the variation (not the exact same)

Keep the **emotional arc** (sad and alone → tear → looks up / prays → warm light → peaceful smile) and the **locked character**. Change **at least three** of these:
- **Setting / prop:** e.g. tree → old fence post, rock, bench, lamppost, hilltop, riverside or window ledge. If the reference has a large tree, ban "a large tree" in DO NOT INCLUDE.
- **Pose:** standing ↔ sitting, or which hand leans on what.
- **Camera direction:** if the reference pulls out, push in, and vice versa. If it's static, use a slow push-in.
- **Light / magic effect:** glow from the hands ↔ rays from the top-left ↔ fireflies ↔ golden dust ↔ a moonbeam through the clouds.
- **Time of day / sky:** night ↔ late dusk ↔ blue hour, stars or no stars.
- **The order or style of the gestures:** cupped hands → prayer, or a hand on the chest → prayer.

Present this in the reply as a short "How your version is different" bullet list.

## Step 5: Frame it "not too zoomed" (mandatory)

The CAMERA section of every prompt must contain all of these rules:
- **One slow, smooth, continuous move** with no cuts. The move should be clearly noticeable and ease out at the end.
- **START:** a wide shot, the full body head to feet, the character about **one-third of the frame height**, sitting in the lower half of the frame.
- **END:** no closer than a **medium shot (waist-up or knees-up)**, with the whole head and the praying hands visible.
- At the closest point the character covers only about **half the frame width**, with **at least 20% of the frame width of sky/background on each side**.
- The **top 25% of the frame stays open, empty sky for the whole video**: this is where the user adds captions later. The head stays below it.
- The head and hands must **never touch or be cut off by any frame edge**.
- "Never closer than waist-up: no close-ups, no face-filling shots. No shake, no rotation, no zoom jumps, no cuts."
- Repeat "close-ups, the character filling the screen, or his head or hands cut off by the frame" in DO NOT INCLUDE.

Each beat in the TIMELINE should also say the shot size at that point (e.g. "wide", "full body, about half the frame height", "knees up"), so the zoom is steady and doesn't jump in early.

## Step 6: Write the Gemini prompt

Defaults: **9:16 vertical, 10 seconds, 24 fps, one continuous shot.** Use the reference's length only if the user asks for it.

Use exactly this section order: **intro line → CHARACTER → SETTING → TIMELINE → CAMERA → LIGHTING AND COLOUR → MOTION QUALITY → AUDIO → DO NOT INCLUDE**.

### Locked CHARACTER block (copy verbatim unless the user gives a new character sheet)

```
CHARACTER (identical in every frame):
* A simple cartoon stick-figure man. Large, perfectly round head, about as wide as his shoulders. Slim, soft, rounded body with a slight hunch. Thin short neck. Long thin arms and legs with smooth joints. Mitten-like hands with a thumb and simple finger lines. Small rounded feet.
* No clothes, no hair, no nose, no ears.
* Skin: flat blue-grey (#888ea3) with a thin pale lavender rim light (#c9cee6) along his LEFT edge and a soft darker blue-grey band (#6d7690) along his RIGHT edge.
* Outline: clean, even, dark slate grey (#363a45), the same thickness everywhere.
* Face placed LOW on the head: two white almond eyes with heavy, flat, drooping upper lids covering the top half, tiny black pupils just under the lids, soft grey bags under the eyes, thin worried eyebrows, and a tiny short mouth line turned slightly down.
* Personality: tired, quiet, gentle, holding in his sadness. Small, slow, subtle movements only. Never bouncy or exaggerated.
```

### Prompt rules
- Intro line: "Create a 10-second VERTICAL 9:16 video, 24 fps, in the style of a soft 2D hand-drawn cartoon: flat colours, clean even dark outlines, gentle cel shading, and a painted, slightly blurred background. One continuous shot with NO cuts."
- **TIMELINE:** 4–5 beats with second ranges. Give tear colour #a7cef3. **No smile before the final beat:** the smile appears only in the last ~1.5 s. The mouth stays a downturned line until then.
- **LIGHTING:** cool, dim blue/purple at first; the warm golden light becomes the main light only in the last ~3.5 s, "change slowly, never suddenly".
- **MOTION QUALITY:** "Smooth, slow, gentle animation, like a calm emotional animated short. No jitter, no morphing, no flicker. His proportions and face design never change."
- **AUDIO:** a soft, slow, muffled sad piano in a minor key with reverb, plus an ambience to match the setting (wind, crickets, water). The music warms when the light appears. **NO voice, dialogue, singing or narration.**
- **DO NOT INCLUDE:** any text, captions, subtitles, letters, numbers, watermarks or logos; extra characters, realistic humans, 3D rendering or photorealism; clothes, hair or a nose on the character; the reference's signature prop (e.g. "a large tree beside the character"); close-ups, the character filling the screen, or his head or hands cut off by the frame; fast motion, cuts or scene changes.
- Never put caption text in the prompt: Gemini garbles text. The user adds it in an editor in the top 25%.

## Step 7: The reply

1. One line: what you analysed (duration, resolution, fps).
2. **What happens in the reference:** the breakdown table + the overlay/framing note.
3. **How your version is different:** 3–4 bullets (camera direction, framing, setting, empty caption space).
4. **Gemini prompt:** the full prompt between `---` rules, as plain text ready to copy.
5. A short note: add captions in the editor in the top 25%. If the reference had a generator watermark, mention that prompt wording usually can't remove it.

Keep the user's character, rules and tone. Write in clear, simple English.

See `references/example-tree-to-fence.md` for a complete worked example (tree reference → fence-post variation) that the user approved.
