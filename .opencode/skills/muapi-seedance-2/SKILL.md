---
slug: muapi-seedance-2
name: muapi-seedance-2
version: "0.3.0"
description: Expert Cinema Director skill for Seedance 2.0 (ByteDance) — high-fidelity video generation across Chinese, Global, and VIP tiers. Supports text-to-video, image-to-video, first-last-frame, omni reference, character training, omni-reference training, video editing, and watermark removal.
acceptLicenseTerms: true
---

# 🎬 Seedance 2.0 Cinema Expert

**The definitive skill for "Director-Level" AI video orchestration.**
Seedance 2.0 is not a descriptive model; it is an *instructional* model. It responds best to technical cinematography, physics directives, and precise camera grammar.

## Core Competencies

1.  **Text-to-Video (t2v)**: Generate cinematic video from a Director Brief — Chinese, Global, or VIP tier.
2.  **Image-to-Video (i2v)**: Animate 1–9 reference images — Chinese, Global (smart mode), or VIP tier.
3.  **Video Extension (extend)**: Seamlessly continue an existing Seedance 2.0 video (Chinese tier).
4.  **First & Last Frame (first-last)**: Interpolate a fluid video between a start image and end image (Global/VIP).
5.  **Omni Reference (omni)**: Full multimodal reference with images + audio + character refs (all tiers).
6.  **Omni Reference Training (omni-train)**: Train a custom persistent character for identity-consistent generation.
7.  **Character Sheet (character)**: Build a reusable character from 1–3 images (Chinese tier).
8.  **Video Edit (video-edit)**: Edit an existing video with a prompt + optional reference images (Chinese tier).
9.  **Watermark Removal (watermark-remove)**: Strip Seedance 2.0 watermarks (basic or Pro).

---

## 🏷️ Tiers

| Tier | Flag | Censorship | Aspect Ratios | Duration | Quality param |
|:---|:---|:---|:---|:---|:---|
| **Chinese** (default) | `--tier chinese` | Low | 16:9, 9:16, 4:3, 3:4 | 5 / 10 / 15 s | Yes (basic/high) |
| **Global** | `--tier global` | Standard | + 21:9, 1:1 | Any 4–15 s | No |
| **VIP** | `--tier vip` | Low | + 21:9, 1:1 | Any 4–15 s | No |

Add `--fast` to any Global or VIP call to use the fast-queue variant (lower latency, same quality).

---

## 📥 Input Limits

| Input Type | Chinese i2v/omni | Global/VIP i2v/omni | Formats | Max Size |
|:---|:---|:---|:---|:---|
| Images | ≤ 9 | ≤ 9 | jpeg, png, webp | 30 MB each |
| Videos | ≤ 3 (omni only) | Not supported | mp4, mov | 50 MB each |
| Audio | ≤ 3 | ≤ 3 | mp3, wav | 15 MB each |
| **First-Last** | — | 1–2 images | jpeg, png, webp | 30 MB each |
| **Video Edit** | 1 video + ≤ 9 imgs | — | mp4 ≤ 10 MB / 15s | — |

**Output**: 4–15 seconds, auto-generated sound, 480p–720p.

---

## ⚠️ Restrictions

- **No realistic human faces** in uploaded images/videos (except character/omni-train modes).
- `--mode extend` requires a `request_id` from a prior `seedance-v2.0-t2v` or `seedance-v2.0-i2v` job.
- `--mode first-last` requires `--tier global` or `--tier vip`.
- Global/VIP omni does **not** support video references (images + audio only).
- `--quality` applies to Chinese tier only.

---

## 🔗 Core Syntax: The @ Reference System

Assign explicit roles to each uploaded asset. Tags differ by mode.

### Chinese Tier (i2v, omni)
```
@image1  @image2  ...  @image9    (images_list order)
@video1  @video2  @video3         (video_files order)
@audio1  @audio2  @audio3         (audio_files order)
```

### Global/VIP Omni (omni-reference-no-video / vip-omni-reference)
```
@image1  @image2  ...  @image9    (images_list order)
@audio1  @audio2  @audio3         (audio_files order)
```

### Character References (all tiers)
```
@character:<request_id>            — from seedance-2-character or completed t2v/i2v job
@omni-character:<character_id>     — from seedance-2-omni-reference-train output
```

### Role Assignment Table

| Purpose | Example Syntax |
|:---|:---|
| First frame | `@Image1 as the first frame` |
| Last frame | `@Image2 as the last frame` |
| Character appearance | `@Image1's character as the subject` |
| Scene / background | `scene references @Image3` |
| Camera movement | `reference @Video1's camera movement` |
| Action / motion | `reference @Video1's action choreography` |
| Visual effects | `completely reference @Video1's effects and transitions` |
| Rhythm / tempo | `video rhythm references @Video1` |
| Voice / tone | `narration voice references @Video1` |
| Background music | `BGM references @Audio1` |
| Sound effects | `sound effects reference @Video3's audio` |
| Outfit / clothing | `wearing the outfit from @Image2` |
| Product appearance | `product details reference @Image3` |

### Multi-Reference Combination
```
@Image1's character as the subject, reference @Video1's camera movement
and action choreography, BGM references @Audio1, scene references @Image2
```

---

## 🏗️ Technical Specification: The Director Brief

Structure prompts using this six-component hierarchy. Order matters — composition first, texture and micro-motion last:

| Component | Instruction Type | Example |
|:---|:---|:---|
| **Scene** | Environment + Lighting | "A rain-soaked cyberpunk street, magenta neon reflections on wet asphalt." |
| **Subject** | Identity + Detail | "A woman in a black trenchcoat, determined focus, cinematic skin textures." |
| **Action** | Fluid Interaction | "Walking forward through the crowd, coat billowing slightly in the wind." |
| **Camera** | Movement + Lens + Speed | "Medium tracking shot, 35mm lens, slow dolly backward over 6s. Subtle handheld jitter." |
| **Audio** | Music + SFX + Ambience | "Low ambient hum, distant traffic, single piano note at 5s. No dialogue." |
| **Pacing/Style** | Timing + Mood + Grade | "Cinematic epic, warm color grade, shallow DOF. Slow build — single action only, no scene cuts." |

> **Seedance 2.0 generates audio natively.** Always include an Audio directive — even one sentence. Without it the model generates random ambient sound that may not match your scene.

### Time-Segmented Prompts (Recommended for 10s+ videos)
Break prompts into timed segments for precise control:
```
0–3s: [opening scene, camera move, establishing action]
3–6s: [mid-section development, subject in motion]
6–10s: [climax or key action beat]
10–15s: [resolution, brand/product hold, text/tagline fade in]
```

> **Single-beat rule:** Each segment should contain one action. 4–7s = one beat. 10–15s = 3–4 beats maximum. Overloading a segment with multiple narrative changes degrades output quality.

### Negative Prompting

Seedance 2.0 supports appending negative guidance directly in the prompt. Use plain language at the end:

```
[your director brief above]
Avoid: camera shake, jump cuts, lens distortion, overexposure, watermarks, text overlays.
```

Common negative additions:
- `Avoid: abrupt cuts, scene changes, multiple locations.` (for single-take shots)
- `Avoid: human faces, realistic people.` (for product-only content)
- `Avoid: fast motion, blur, unstable framing.` (for smooth product reveals)

---

## 🎥 Camera Language Reference

### Basic Movements
| Term | Description |
|:---|:---|
| Push in / Slow push | Camera moves toward subject |
| Pull back / Pull away | Camera moves away from subject |
| Pan left/right | Camera rotates horizontally |
| Tilt up/down | Camera rotates vertically |
| Track / Follow shot | Camera follows subject movement |
| Orbit / Revolve | Camera circles around subject |
| One-take / Oner | Continuous shot with no cuts |

### Advanced Techniques
| Term | Description |
|:---|:---|
| Hitchcock zoom (dolly zoom) | Push in + zoom out — creates vertigo effect |
| Fisheye lens | Ultra-wide distorted lens |
| Low angle / High angle | Camera below/above subject |
| Bird's eye / Overhead | Top-down view |
| First-person POV (FPV) | Immersive subjective camera from character/object's eyes — GoPro-style wide angle, forward motion, no cuts |
| Drone flythrough | Cinematic aerial descent — gimbal-stabilized, sweeping lateral arc, DJI Inspire aesthetic |
| Architectural flythrough | Ground-level continuous dolly through connected spaces — one-take, practical lighting |
| Whip pan | Very fast horizontal pan with motion blur |
| Crane shot | Vertical movement like a crane arm |

### Shot Sizes
| Term | Description |
|:---|:---|
| Extreme close-up | Eyes, mouth, or small detail only |
| Close-up | Face fills frame |
| Medium close-up | Head and shoulders |
| Medium shot | Waist up |
| Full shot | Entire body |
| Wide / Establishing shot | Full environment |

---

## 🧠 Prompt Optimization Protocol

**The Agent MUST transform user intent into a technical "Director Brief" before execution.**

1.  **Technical Grammar**: Use camera terms: *Dolly In/Out, Crane Shot, Whip Pan, Tracking Shot, Anamorphic Lens, Shallow Depth of Field, High-Speed Dive, Orbital Arc*.
2.  **Physics Directives**: Use "caustic patterns," "volumetric rays," or "subsurface scattering" instead of "good lighting."
3.  **Timecode Notation**: For multi-beat scenes, use `[00:00-00:05s]` format to specify timing.
4.  **Tag References**: If files provided, use: *"Replicate the camera movement of @video1 while maintaining the visual style of @image1."* (lowercase, 1-based index)
5.  **ORDER MATTERS**: Tokens at the start define composition; tokens at the end define texture and micro-motion.
6.  **Multi-Image i2v**: Provide up to 9 reference images. The model blends aspects (style, identity, environment) across all inputs.
7.  **Audio is mandatory**: Seedance 2.0 generates audio natively. Always include an Audio line — music genre/tone, key SFX, ambient texture. Silent direction = random audio.
8.  **Single-beat discipline**: Each timed segment = one action. Cramming two narrative beats into 4s degrades physics and motion consistency.

---

## 🎭 Capability-Specific Patterns

### 1. Character Consistency
```
The man in @Image1 walks tiredly down the hallway, slowing his steps,
finally stopping at his front door. Close-up on his face — he takes a
deep breath, replaces the weariness with a relaxed expression.
Maintain high character consistency, zero facial flicker, persistent clothing details.
```

### 2. Camera Movement Replication
```
Reference @Image1's male character. He is in @Image2's elevator.
Completely reference @Video1's camera movements and facial expressions.
Hitchcock zoom during the fear moment, then orbit shots of the interior.
Elevator doors open, follow shot walking out.
```

### 3. Video Extension (Forward)
```
Extend @Video1 by 10 seconds.
1–5s: Light and shadow slowly slide across table through venetian blinds.
6–10s: A coffee bean drifts down. Camera pushes in toward it until screen goes black.
English text gradually appears — "Lucky Coffee", "Breakfast", "AM 7:00-10:00".
```

### 4. Video Extension (Reverse / Prepend)
```
Extend backward 10s. In warm afternoon light, the camera starts from
the corner with awning fluttering in the breeze, slowly tilting down
to flowers peeking out at the wall base, building anticipation for the main scene.
```

### 5. Video Editing (Modify Existing)
```
Subvert @Video1's plot — the character's expression shifts from warmth to
cold determination. The action is decisive, without hesitation.
Maintain all other visual elements (scene, lighting, timing).
```

### 6. Music Beat-Matching
```bash
bash scripts/generate-seedance.sh \
  --mode i2v \
  --file img1.jpg --file img2.jpg --file img3.jpg \
  --video-file reference_edit.mp4 \
  --audio-file track.mp3 \
  --subject "@Image1 @Image2 @Image3 — match the keyframe positions and rhythm of @Video1 for beat-synced cuts. BGM references @Audio1. More dynamic movement, dreamlike visual style." \
  --duration 15 --quality high
```

### 7. Dialogue / Voice Acting
```
In the "Cat & Dog Roast Show" — emotionally expressive comedy segment:
Cat host (licking paw, rolling eyes): "Who understands my suffering?"
Dog host (head tilted, tail wagging): "You're one to talk? You sleep 18 hours a day..."
Sound: lively studio ambience, audience laughter, punchy transitions.
```

### 8. One-Take / Long Take
```
@Image1 @Image2 @Image3 — one-take tracking shot following a runner
from the street up stairs, through a corridor, onto a rooftop,
finally overlooking the city. No cuts throughout.
```

### 9. E-commerce / Product Showcase
```bash
bash scripts/generate-seedance.sh \
  --mode i2v \
  --file product.jpg \
  --subject "Deconstruct the product. Static camera. Hamburger suspended mid-air, rotating slowly. Ingredients separate and reassemble. Cheese continues to melt and drip. Ultimate food aesthetics." \
  --intent "product" \
  --aspect "9:16" \
  --duration 15 --quality high
```

### 10. Science / Educational Visualization
```bash
bash scripts/generate-seedance.sh \
  --subject "15-second health educational clip. 0–5s: Transparent blue human upper body, camera pushes into a clear artery, blood flows smoothly. 5–10s: Sugar and fat particles enter bloodstream, lipid deposits form on vessel walls. 10–15s: Vessel narrows, before/after comparison. 4K medical CGI, semi-transparent visualization." \
  --intent "educational" \
  --duration 15 --quality high
```

### 11. FPV First-Person Shot
```bash
bash scripts/generate-seedance.sh \
  --subject "Immersive first-person POV shot. Camera glides at eye level through a narrow mountain trail,
trees rushing past in peripheral blur, rocky terrain below. Slight natural stabilization with wide-angle lens.
Continuous forward motion, no cuts throughout. Trail opens into a clearing — mountain peak visible ahead.
Sound: wind, footsteps on gravel, distant birds. Natural ambient audio, no music." \
  --intent "fpv" \
  --aspect "9:16" --duration 10 --quality high
```

### 12. Cinematic Drone Flythrough
```bash
bash scripts/generate-seedance.sh \
  --subject "Cinematic aerial drone shot. Camera starts at 150m altitude above a coastal city at golden hour.
Smooth gimbal-stabilized descent along a sweeping lateral arc, dropping toward a rooftop terrace.
Long shadows cast across building tops, warm light on ocean surface. High-speed dive closes in
to product on the terrace — final frame settles into a medium close-up.
Sound: gentle wind, distant city hum, soft cinematic score building to resolve." \
  --intent "drone" \
  --aspect "16:9" --duration 10 --tier global --view
```

---

## 🎨 Prompt Templates

### Cinematic Film
```
[SCENE] Rain-soaked cyberpunk alley, neon signs reflected on wet cobblestones.
[SUBJECT] A lone figure in a weathered trench coat, face obscured by a wide-brim hat.
[ACTION] Walking slowly, each step splashing neon color into the puddles.
[CAMERA] Low-angle tracking shot, anamorphic lens, slow dolly in. Rack focus to face.
[STYLE] Denis Villeneuve aesthetic, high contrast, desaturated blues and magentas. 24fps.
```

### Product Ad (15s)
```
Reference @Video1's editing style. Replace @Video1's product with @Image1 as hero.
0–3s: Product enters with dynamic rotation, close-up on surface texture and logo.
4–8s: Multiple angle transitions — front, side, back — with highlight scanning light.
9–12s: Product in lifestyle context showing usage.
13–15s: Hero shot with brand tagline, background music builds to resolution.
Sound: Reference @Video1's BGM. Add product interaction sound effects.
```

### Short Drama (15s)
```
Scene (0–5s): Close-up on character's reddened eyes, finger pointing accusingly.
Dialogue 1: "What exactly are you trying to take from me?"
Scene (6–10s): Other character trembles, holding up evidence, steps forward.
Dialogue 2: "I'm not deceiving you! This is what he entrusted to me!"
Scene (11–15s): Evidence revealed, first character freezes — anger shifts to shock.
Sound: Urgent piano + static interference, sobbing, muffled voice blending in.
Duration: Precise 15 seconds, every frame tight, no filler.
```

### Dance / Beat-Sync (13s)
```
Have the character in @Image1 replicate the dance moves and beat-synced
music from @Video1. Generate a 13-second video. Movements should be
smooth with no stuttering or freezing.
```

### Scenery Montage (15s)
```
@Image1 @Image2 @Image3 @Image4 @Image5 @Image6 — landscape scene images.
Reference @Video1's visual rhythm, inter-scene transitions, visual style,
and music tempo for beat-synced editing.
```

### Advertising / Product Motion
```
[SCENE] Minimalist white studio, single product on a rotating pedestal.
[ACTION] Subtle 360° rotation, product details catching specular highlights.
[CAMERA] Tight medium shot, macro lens pass over surface texture, slow orbit.
[STYLE] Commercial grade, perfect exposure, zero background distraction.
```

### Action / Physics
```
[SCENE] Desert canyon at sunrise, sandy terrain, long shadows.
[SUBJECT] High-performance sports car accelerating through a turn.
[ACTION] Rear wheels spinning with dust plume, chassis flexing under g-force.
[CAMERA] Low hero angle dolly tracking alongside, then whip pan to lead car.
[STYLE] Hollywood racing film, warm golden grade, motion blur on wheels. 24fps.
```

### Character Consistency (Martial Arts)
```
[SUBJECT] Same fighter throughout: young woman, white gi, black belt, determined expression.
[ACTION] Fluid kata sequence — rising block, stepping side kick, spinning back fist.
[CAMERA] Full-body wide shot, then cut to close-up of fist impact in slow motion.
[STYLE] Maintain identical lighting, clothing, and facial features in every frame. Zero flicker.
```

---

## 🎚️ Style & Quality Modifiers

### Visual Style
- `Cinematic quality, film grain, shallow depth of field`
- `2.35:1 widescreen, 24fps`
- `Ink wash painting style` / `Anime style` / `Photorealistic`
- `High saturation neon colors, cool-warm contrast`
- `4K medical CGI, semi-transparent visualization`

### Mood / Atmosphere
- `Tense and suspenseful` / `Warm and healing` / `Epic and grand`
- `Comedy with exaggerated expressions`
- `Documentary tone, restrained narration`

### Audio Direction
- `Background music: grand and majestic`
- `Sound effects: footsteps, crowd noise, car sounds`
- `Voice tone reference @Video1`
- `Beat-synced transitions matching music rhythm`

---

## ❌ Common Mistakes to Avoid

1. **Vague references**: Don't say "reference @Video1" — specify WHAT to reference (camera? action? effects? rhythm?)
2. **Conflicting instructions**: Don't ask for "static camera" and "orbit shot" in the same segment.
3. **Overloading**: Don't pack too many scenes into 4–5 seconds — keep it physically plausible.
4. **Missing @ assignments**: If you upload 5 images, make sure each one is referenced with a clear purpose.
5. **Ignoring audio**: Sound design dramatically improves output — always include audio direction.
6. **Forgetting duration**: Match prompt complexity to the selected generation length.
7. **Real faces**: Don't upload real human photos — the system will block them.
8. **Keyword soup**: DO NOT use "8k, masterpiece, trending." Use technical descriptions instead.
9. **Discontinuous action**: Avoid "The man runs and then he stops." Use fluid transitional language.
10. **Missing audio direction**: Seedance 2.0 generates audio natively — always specify music tone, SFX, or ambience. Skipping it produces random sound.
11. **Narrative overload per segment**: Each timed segment should contain one action beat. Multiple scene changes in 4s produce degraded physics and motion artifacts.
12. **FPV without continuous motion**: FPV requires a motion-rich environment to work — a static room with FPV intent will not trigger the immersive effect. Pair FPV with corridors, streets, natural terrain, or product flyovers.
13. **Drone without a destination**: Drone shots need a resolve point — specify what the camera descends toward or arrives at. "Drone shot" alone produces aimless floating.

---

## 🚀 Protocol: All Modes

### Mode 1: Text-to-Video (t2v)

```bash
# Chinese tier (default) — epic reveal
bash scripts/generate-seedance.sh \
  --subject "hidden Andes temple, mist through the canopy" \
  --intent epic --aspect "16:9" --duration 10 --quality high --view

# Global tier — 21:9 cinematic, 12s
bash scripts/generate-seedance.sh \
  --tier global \
  --subject "neon cyberpunk alley, rain-slicked streets" \
  --intent tense --aspect "21:9" --duration 12 --view

# VIP fast — square social format
bash scripts/generate-seedance.sh \
  --tier vip --fast \
  --subject "product rotating on a pedestal, specular highlights" \
  --intent product --aspect "1:1" --duration 6
```

### Mode 2: Image-to-Video (i2v)

```bash
# Chinese tier — animate with video/audio refs
bash scripts/generate-seedance.sh --mode i2v \
  --file character.jpg --video-file ref_motion.mp4 --audio-file bgm.mp3 \
  --subject "@image1's character walks forward, @video1's camera movement, BGM references @audio1" \
  --quality high --view

# Global tier — 1 image = first frame anchor
bash scripts/generate-seedance.sh --mode i2v --tier global \
  --file hero.jpg \
  --subject "hero strides forward, coat billowing in slow motion" \
  --duration 8 --view

# VIP fast — 3 images, omni ref mode (2-9 images switches to omni)
bash scripts/generate-seedance.sh --mode i2v --tier vip --fast \
  --file char.jpg --file env.jpg --file style.jpg \
  --subject "@image1 character walks through @image2's environment in @image3's style" \
  --duration 10
```

### Mode 3: Extend Video (Chinese tier)

```bash
# Extend naturally
bash scripts/generate-seedance.sh --mode extend \
  --request-id "abc-123-def-456" --duration 10

# Extend with directional prompt
bash scripts/generate-seedance.sh --mode extend \
  --request-id "abc-123-def-456" \
  --subject "camera continues pulling back, revealing the vast city below" \
  --intent reveal --duration 10 --quality high --view
```

### Mode 4: First & Last Frame (Global/VIP)

```bash
# One image = first frame anchor
bash scripts/generate-seedance.sh --mode first-last --tier global \
  --file opening_scene.jpg \
  --subject "smooth cinematic push into the scene" --duration 6 --view

# Two images = interpolate between first and last frame
bash scripts/generate-seedance.sh --mode first-last --tier vip --fast \
  --file start.jpg --file end.jpg \
  --subject "dramatic reveal transition between the two frames" --duration 8 --view
```

### Mode 5: Omni Reference (omni)

```bash
# Chinese tier — images + video + audio refs
bash scripts/generate-seedance.sh --mode omni --tier chinese \
  --file character.jpg --video-file ref_edit.mp4 --audio-file track.mp3 \
  --subject "@image1's character performs moves from @video1, BGM references @audio1" \
  --duration 15 --quality high --view

# Global tier — images + audio, no video refs
bash scripts/generate-seedance.sh --mode omni --tier global \
  --file portrait.jpg --audio-file bgm.mp3 \
  --subject "@image1 is the main character. Walking through a neon-lit city at night. BGM references @audio1." \
  --aspect "16:9" --duration 8 --view

# VIP tier — with trained omni character
bash scripts/generate-seedance.sh --mode omni --tier vip --fast \
  --subject "@omni-character:char_1775422630065_4vbana walks through a garden at golden hour" \
  --aspect "16:9" --duration 10 --view

# With @character ref (from character mode)
bash scripts/generate-seedance.sh --mode omni --tier global \
  --subject "@character:cab9517f-1818-4910-8d66 walks down a rain-soaked alley, cinematic tracking shot" \
  --duration 8 --view
```

### Mode 6: Train Omni Reference Character (omni-train)

```bash
# Train from a single portrait
bash scripts/generate-seedance.sh --mode omni-train \
  --file portrait.jpg \
  --character-name "Alex" \
  --character-desc "A brave explorer with piercing blue eyes"

# Use in omni prompts after training completes:
# @omni-character:<character_id returned>
```

### Mode 7: Character Sheet (character, Chinese tier)

```bash
# Build character from 1–3 reference images
bash scripts/generate-seedance.sh --mode character \
  --file ref1.jpg --file ref2.jpg \
  --character-name "Hero" \
  --subject "red leather jacket with black jeans and white sneakers"

# Use the returned request_id in t2v/i2v/omni:
# @character:<request_id>
```

### Mode 8: Video Edit (video-edit, Chinese tier)

```bash
# Replace subject in an existing video
bash scripts/generate-seedance.sh --mode video-edit \
  --video-url "https://example.com/input.mp4" \
  --file replacement_character.jpg \
  --subject "Replace the running man with @image1. Preserve exact motion, speed, and camera shake." \
  --quality high --view

# Edit with watermark removal in one step
bash scripts/generate-seedance.sh --mode video-edit \
  --video-file source.mp4 \
  --subject "Subvert the plot — the character's expression shifts from warmth to cold determination." \
  --remove-watermark --view
```

### Mode 9: Watermark Removal (watermark-remove)

```bash
# Basic watermark removal
bash scripts/generate-seedance.sh --mode watermark-remove \
  --video-url "https://example.com/seedance_output.mp4" --view

# Pro watermark removal (100MB limit, better quality)
bash scripts/generate-seedance.sh --mode watermark-remove \
  --video-file my_video.mp4 --pro --view
```

### Async Pattern

```bash
# Submit and get request_id immediately
RESULT=$(bash scripts/generate-seedance.sh --tier vip --fast --subject "..." --async --json)
REQUEST_ID=$(echo "$RESULT" | jq -r '.request_id')

# Check status later
bash ../../../../core/media/generate-video.sh --result "$REQUEST_ID"
```

---

## ⚙️ Implementation Details

### Endpoint Reference

| Mode | Tier | Endpoint |
|:---|:---|:---|
| `t2v` | chinese | `seedance-v2.0-t2v` |
| `t2v` | global | `seedance-2-text-to-video{-fast}` |
| `t2v` | vip | `seedance-2-vip-text-to-video{-fast}` |
| `i2v` | chinese | `seedance-v2.0-i2v` |
| `i2v` | global | `seedance-2-image-to-video{-fast}` |
| `i2v` | vip | `seedance-2-vip-image-to-video{-fast}` |
| `extend` | chinese | `seedance-v2.0-extend` |
| `first-last` | global | `seedance-2-first-last-frame{-fast}` |
| `first-last` | vip | `seedance-2-vip-first-last-frame{-fast}` |
| `omni` | chinese | `seedance-2.0-omni-reference` |
| `omni` | global | `seedance-2-omni-reference-no-video{-fast}` |
| `omni` | vip | `seedance-2-vip-omni-reference{-fast}` |
| `omni-train` | any | `seedance-2-omni-reference-train` |
| `character` | any | `seedance-2-character` |
| `video-edit` | chinese | `seedance-v2.0-video-edit` |
| `watermark-remove` | — | `seedance-2.0-watermark-remover` / `seedance-2-video-watermark-remover-pro` |

### Parameter Differences by Tier

| Parameter | Chinese | Global | VIP |
|:---|:---|:---|:---|
| `aspect_ratio` | 16:9, 9:16, 4:3, 3:4 | + 21:9, 1:1 | + 21:9, 1:1 |
| `duration` | 5 / 10 / 15 (enum) | 4–15 (any int) | 4–15 (any int) |
| `quality` | basic / high | — (not supported) | — (not supported) |
| `video_files` (omni) | ✅ up to 3 | ❌ | ❌ |
| `audio_files` (omni) | ✅ up to 3 | ✅ up to 3 | ✅ up to 3 |
| Fast variant | ❌ | ✅ (`--fast`) | ✅ (`--fast`) |

This skill acts as a **Cinematographic Wrapper** that translates creative intent into high-fidelity technical instructions for the `muapi` core.
