---
slug: muapi-social-media-video
name: muapi-social-media-video
version: "1.0.0"
description: Brand-aware social media video creator. Reads brand-identity.md, ICP.md, and messaging.md to write a post/storyboard, craft an optimized Seedance 2.0 Director prompt, generate reference frames with the best available image model, and produce platform-ready video.
acceptLicenseTerms: true
---

# Social Media Video Creator

**End-to-end pipeline: Brand Files → Storyboard → Reference Images → Seedance 2.0 Video.**

Reads your brand identity, ICP, and messaging documents to produce on-brand social video — fully optimized for Seedance 2.0's instructional prompt grammar and your target platform.

---

## Agent Execution Protocol

### Step 1 — Read Brand Files

Before writing anything, the agent MUST read all available brand files. Look for them in the working directory or any `brand/` subdirectory:

| File | What to extract |
|:---|:---|
| `brand-identity.md` | Visual style, color palette, tone, logo/product aesthetics, brand personality |
| `ICP.md` | Target audience — who they are, their pain points, what motivates them |
| `messaging.md` | Core value props, hooks, CTAs, campaign themes, taglines |

If a file is missing, proceed with what's available and note the gap.

---

### Step 2 — Write the Social Post + Storyboard

Use brand context to produce:

**Social Post Copy** (for caption/copy):
- Hook line (first 1–2 sentences — must stop the scroll)
- Body (3–5 sentences: problem → solution → proof → CTA)
- Hashtags (5–8 relevant tags)
- CTA (one clear action)

**Storyboard** (match duration to platform spec):
```
0–3s:  [Opening scene — hook/visual surprise — camera move]
3–7s:  [Core message — product/subject in action — camera move]
7–10s: [Resolution — brand moment — CTA text on screen]
10–15s: [Logo/tagline hold — background music fade]
```

Tone must match brand personality. If brand is playful → upbeat transitions, bright grade.
If brand is premium → slow reveals, dark luxury aesthetic, moody lighting.

---

### Step 3 — Craft the Seedance 2.0 Director Prompt

Transform the storyboard into a **technical Director Brief** for Seedance 2.0.

**Rules:**
1. Never use vague descriptors ("beautiful", "amazing", "8k"). Use technical cinematography language.
2. Always specify camera movement, lens type, and lighting physically.
3. For 10s+ videos, use timecode segments: `0–3s: [...] 3–7s: [...] 7–10s: [...]`
4. Integrate `@image1`, `@image2` reference tags if images are provided.
5. Always include sound direction (even brief) — Seedance generates audio.
6. Lead with composition, end with texture and micro-motion.

**Director Brief Template:**
```
[SCENE] {environment, lighting, time of day}
[SUBJECT] {product/character/subject with specific detail}
[ACTION] {what happens — fluid, continuous, physically plausible}
[CAMERA] {movement + lens + framing}
[STYLE] {color grade, mood, film reference if helpful}
[SOUND] {music tone, sound effects, ambient}
0–Xs: {if multi-beat}
```

---

### Step 4 — Generate Reference Images (If Needed)

**When to generate reference images:**

| Scenario | Mode | Images Needed |
|:---|:---|:---|
| Product showcase | `i2v` | 1 product shot as first frame |
| Scene transition | `first-last` | 2 images — opening and closing frame |
| Brand character | `i2v` | 1 character reference |
| Pure concept | `t2v` | None — text only |
| Mood/style anchor | `i2v` | 1 style reference image |

**Image generation — best models by use case:**

| Use Case | Recommended Model | Why |
|:---|:---|:---|
| Photorealistic product/scene | `google-imagen4-ultra` | Highest realism, great lighting |
| Concept art / stylized | `flux-kontext-pro-t2i` | Creative fidelity, style adherence |
| Fastest turnaround | `google-imagen4-fast` | Speed with good quality |
| Highly detailed/editorial | `hidream-i1-full` | Fine detail, editorial quality |
| Character with identity | `ideogram-v3-t2i` | Strong text + character rendering |

**Reference image prompt format:**
Write a clean, technical image prompt (not a Seedance prompt). Include:
- Subject description + key product/brand visual elements
- Lighting (studio, golden hour, etc.)
- Shot framing (medium shot, product close-up, etc.)
- Mood/color palette matching brand identity
- NO motion language (this is for a still frame)

Execute image generation:
```bash
bash core/media/generate-image.sh \
  --model google-imagen4-ultra \
  --prompt "your image prompt" \
  --aspect-ratio 9:16 \
  --view
```

---

### Step 5 — Generate the Video

Choose mode, tier, and camera based on content type and available assets.

**Mode selection:**

| Situation | Mode | Command |
|:---|:---|:---|
| No reference images | `t2v` | default |
| 1 image (first frame) | `i2v` | `--mode i2v --file ref.jpg` |
| Start + end frames | `first-last` | `--mode first-last --tier global --file start.jpg --file end.jpg` |
| Multi-ref blend | `i2v` | up to 9 images |

**Invoke the script:**
```bash
bash library/social/social-media-video/scripts/run-social-video.sh \
  --prompt "your director brief here" \
  --platform instagram \
  --camera drone \
  [--mode t2v|i2v|first-last] \
  [--file ref_image.jpg] \
  [--gen-ref "reference image prompt"] \
  [--tier global] \
  [--quality high] \
  [--view]
```

---

## Platform Specs

| Platform | Format | Aspect | Duration | Notes |
|:---|:---|:---|:---|:---|
| Instagram Reels | Vertical | 9:16 | 10–15s | Hook in first 1s |
| Instagram Feed | Square | 1:1 | 10s | Static-feel works well |
| TikTok | Vertical | 9:16 | 10–15s | High energy, fast cuts |
| YouTube Shorts | Vertical | 9:16 | 15s | Max quality |
| LinkedIn | Landscape | 16:9 | 10–15s | Professional tone |
| Twitter/X | Landscape | 16:9 | 10s | Punchy, direct |
| YouTube (long) | Landscape | 16:9 | 15s | Cinematic, slow builds |
| Pinterest | Portrait | 4:3 | 10s | Lifestyle-forward |

> **Tier note:** Use `--tier global` or `--tier vip` for `1:1` and `21:9` formats. Chinese tier supports only 16:9, 9:16, 4:3, 3:4.

---

## Camera Language Reference

### Standard Camera Intents (--camera / --intent flags)

| Intent | Movement | Best For |
|:---|:---|:---|
| `reveal` | Slow crane up, wide establishing | Product launches, brand reveals |
| `epic` | Dolly in + orbit, low hero angle | Brand manifesto, emotional story |
| `product` | Static macro orbit, precision reveal | E-commerce, product demo |
| `narrative` | Tracking shot, Steadicam | Testimonials, story-driven |
| `tense` | Handheld jitter, dutch angle | High-energy, urgency |
| `comedy` | Reactive handheld, punchy zooms | Lighthearted brand content |

### Specialty Camera Intents (New)

| Intent | Description | Best For |
|:---|:---|:---|
| `fpv` | First-person subjective POV — immersive GoPro-style, continuous forward motion, peripheral detail close-ups | Action brands, travel, sports, tech demos |
| `drone` | Aerial cinematic flythrough — smooth gimbal-stabilized, sweeping laterals, descend from high altitude into scene | Real estate, luxury, outdoor brands, epic reveals |
| `flythrough` | Ground-level architectural flythrough — continuous dolly through space, seamless portal transitions | Architecture, interior design, venue showcases |

**FPV Prompt Enrichment:**
```
Immersive first-person POV shot. Camera glides forward through [scene] at eye level.
Slight natural motion stabilization with GoPro-style wide angle.
Peripheral detail rushing past — [details]. Smooth continuous forward motion.
No cuts throughout. [Subject] visible in foreground periphery.
```

**Drone Flythrough Prompt Enrichment:**
```
Cinematic aerial drone shot. Camera descends from 200m altitude toward [subject/scene],
sweeping lateral arc as it descends. Gimbal-stabilized smooth motion.
Golden hour atmosphere, long shadows across [terrain/scene].
Final frame settles into medium establishing shot.
Aerial cinematography, DJI Inspire aesthetic.
```

---

## Prompt Quality Checklist

Before finalizing the Seedance prompt, verify:

- [ ] Scene environment is physically specific (not "nice background")
- [ ] Camera movement named explicitly (dolly in, orbit, drone flythrough, FPV, etc.)
- [ ] Lighting described technically (volumetric god rays, rim lighting, soft diffused, etc.)
- [ ] Subject/product described with visual specifics from brand-identity.md
- [ ] Sound direction included (even one line)
- [ ] Timecodes used for 10s+ videos
- [ ] `@image1` etc. referenced if images are provided
- [ ] Brand CTA or tagline included in final seconds
- [ ] No vague adjectives ("amazing", "beautiful", "stunning") — replaced with technical terms

---

## Example: Full Workflow

**User**: "Make an Instagram Reel for our cold brew coffee brand, drone shot, premium feel"

**Step 1 — Brand read**: Read `brand-identity.md` (minimalist packaging, dark roast, black + gold palette), `ICP.md` (urban professionals 25–40, values quality), `messaging.md` ("Precision Brewed. Zero Compromise.")

**Step 2 — Storyboard**:
```
0–2s:  Drone descends over rooftop terrace at sunrise, fog below.
2–5s:  Drone swoops down toward coffee cup on white marble, steam rising.
5–9s:  Close-up orbit of bottle, specular gold highlights, ice cubes.
9–11s: Product settles, black screen fades in: "Zero Compromise." + logo
Sound: Minimal lounge beat, coffee pour sound effect at 5s.
```

**Step 3 — Seedance Director Prompt**:
```
0–2s: Cinematic aerial drone shot. Camera descends at 30° angle toward a rooftop terrace at sunrise.
Golden hour atmosphere, San Francisco bay fog below horizon. Gimbal-stabilized smooth descent.
2–5s: Drone rapidly closes toward a glass of cold brew coffee on white marble.
Steam curling upward. Macro lens approach. Caustic light patterns on wet glass surface.
5–9s: Slow precision orbit around cold brew bottle. Black matte label with gold embossed text catching
specular highlights. Ice cubes with subsurface light scattering. Commercial macro aesthetic.
9–11s: Static product hero shot. Letterbox crop, deep focus, black fade in from sides.
Sound: Minimal ambient beat, single piano note, coffee liquid sound effect at 5s mark.
Maintain cinematic color grade — deep blacks, warm gold midtones throughout.
```

**Step 4 — Generate reference**: `google-imagen4-ultra` → cold brew bottle product shot (9:16)

**Step 5 — Generate video**:
```bash
bash library/social/social-media-video/scripts/run-social-video.sh \
  --prompt "0–2s: Cinematic aerial drone shot..." \
  --platform instagram \
  --camera drone \
  --mode i2v \
  --file media_outputs/coldbrew_ref.jpg \
  --duration 11 \
  --tier global \
  --view
```

---

## Common Mistakes to Avoid

1. **Reading brand files and ignoring them** — the storyboard must visually match the brand palette and tone.
2. **Generic prompts** — "a nice video of a product" produces generic output. Every token must direct.
3. **Wrong tier for aspect ratio** — 1:1 and 21:9 require `--tier global` or `--tier vip`.
4. **Forgetting sound** — Seedance generates audio. Direct it, or you get random results.
5. **FPV with static subject** — FPV requires continuous motion in the scene. Pair with movement-rich environments.
6. **Drone without establishing shot** — drone works best when it resolves INTO something (a product, a scene, a subject).
7. **Too many scene changes in 5s** — match complexity to duration. 5s = 1 beat. 15s = 3–4 beats.
