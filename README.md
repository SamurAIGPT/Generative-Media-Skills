# 🎭 Generative Media Skills for AI Agents

**The Ultimate Multimodal Toolset for Claude Code, Cursor, Gemini CLI, and OpenCode.**
A high-performance, schema-driven architecture for AI agents to generate, edit, and display professional-grade images, videos, and audio — powered by the [muapi-cli](https://github.com/SamurAIGPT/muapi-cli).


[🚀 Get Started](#-quick-start) | [🎬 Recipe Pack](#-recipe-pack) | [🎨 Expert Library](#-expert-library) | [⚙️ Core Primitives](#-core-primitives) | [🤖 MCP Server](#-mcp-server) | [📖 Reference](#-schema-reference)

---

## Related Projects

- [Open-Generative-AI](https://github.com/Anil-matcha/Open-Generative-AI) — Free self-hosted AI media studio — GUI alternative to these skills for the same model set
- [Awesome-GPT-Image-2-API-Prompts](https://github.com/Anil-matcha/Awesome-GPT-Image-2-API-Prompts) — Curated GPT-Image-2 prompts to use with these skills
- [Awesome-Gemini-Omni-API-Prompts](https://github.com/Anil-matcha/Awesome-Gemini-Omni-API-Prompts) — Curated Gemini Omni prompts for video generation

## ✨ Key Features

- **🤖 Agent-Native Design** — CLI-powered scripts with structured JSON outputs, semantic exit codes, and `--jq` filtering for seamless agentic pipelines.
- **🧠 Expert Knowledge Layer** — Domain-specific skills that bake in professional cinematography, atomic design, and branding logic.
- **⚡ CLI-Powered Core** — All primitives delegate to [`muapi-cli`](https://www.npmjs.com/package/muapi-cli) — no curl, no JSON parsing, no boilerplate.
- **🖼️ Direct Media Display** — Use the `--view` flag to automatically download and open generated media in your system viewer.
- **📁 Local File Support** — Auto-upload images, videos, faces, and audio from your local machine to the CDN for processing.
- **🌈 100+ AI Models** — One-click access to **Midjourney v7, Flux Kontext, Seedance 2.0, Kling 3.0, Veo3**, and more.
- **🔌 MCP Server** — Run `muapi mcp serve` to expose all 19 tools directly to Claude Desktop, Cursor, or any MCP-compatible agent.

---

## 🏗️ Scalable Architecture

This repository uses a **Core/Library** split to ensure efficiency and high-signal discovery for LLMs:

### ⚙️ Core Primitives (`/core`)
Thin wrappers around [`muapi-cli`](https://github.com/SamurAIGPT/muapi-cli) for raw API access.
- `core/media/` — File upload
- `core/edit/` — Image editing (prompt-based)
- `core/platform/` — Setup, auth & result polling

### 📚 Expert Library (`/library`)
High-value skills that translate creative intent into technical directives.
- **Cinema Director** (`/library/motion/cinema-director/`) — Technical film direction & cinematography.
- **Nano-Banana** (`/library/visual/nano-banana/`) — Reasoning-driven image generation (Gemini 3 Style).
- **UI Designer** (`/library/visual/ui-design/`) — High-fidelity mobile/web mockups (Atomic Design).
- **Logo Creator** (`/library/visual/logo-creator/`) — Minimalist vector branding (Geometric Primitives).
- **Seedance 2 (Doubao Video)** (`/library/motion/seedance-2/`) — Director-level cinematic video generation with text-to-video, image-to-video, and video extension with native audio-video sync.
- **AI Clipping** (`/library/edit/ai-clipping/`) — Long video → ranked vertical short clips in one managed API call. Server-side transcription, virality ranking, dedupe, and face-tracked auto-crop — no local Whisper or LLM.
- **YouTube Shorts** (`/library/social/youtube-shorts/`) — Platform-aware preset over AI Clipping (Shorts / TikTok / Reels / Feed defaults).

Plus **41 ready-to-run workflow recipes** organized by output type — see [🎬 Recipe Pack](#-recipe-pack) below.

---

## 🎬 Recipe Pack

Forty-one LLM-orchestrated workflow recipes that combine multiple `muapi-cli` calls into named end-to-end pipelines (e.g. *photo of person → 3D action figure*, *product photo → cinematic 10s ad*). Each skill is a SKILL.md the agent reads and follows; bring your own consuming agent (Claude Code, Cursor, MCP) — these are recipes, not bash wrappers.

**Motion / Video (16)**

| Skill | Description |
|:---|:---|
| [3D Logo Animation](library/motion/3d-logo-animation/) | Transform a 2D logo into a premium 3D version and animate it with professional cinematic effects |
| [AI Fight Scene Generator](library/motion/ai-fight-scene/) | High-cut-density action / fight scene — 16-cell storyboard image drives Seedance 2.0 i2v for shot-by-shot choreography |
| [Animal Vlogger Video](library/motion/animal-video-generator/) | Hilarious, ultra-realistic anthropomorphic-animal vlogger acting like a human in a real-world setting |
| [Cartoon Dance Animation](library/motion/cartoon-dance-animation/) | Convert a photo into a Pixar-style 3D cartoon, then animate using a reference dance/motion video |
| [Character Story Video](library/motion/character-story-video/) | Multi-part animated story video — establish a consistent character then animate sequential scenes |
| [Drone-Style Video](library/motion/drone-style-video/) | Aerial drone-perspective footage — bird's-eye sweeps, orbit shots, and flyover sequences |
| [Giant Product Showcase](library/motion/giant-product-showcase/) | Dramatic giant-scale product visual (building-sized object next to a person), optionally animated |
| [Jewelry Product Video](library/motion/jewelry-product-video/) | Luxury jewelry ad with high-end commercial cinematography and detailed macro animation |
| [Music Video](library/motion/music-video/) | Short music video from a song theme — keyframes, animation per beat, matching music track |
| [One-Shot Video](library/motion/one-shot-video/) | Single continuous cinematic shot — no cuts, one seamless flowing scene |
| [Cinematic Product Ad](library/motion/product-ad-cinematic/) | Cinematic 5–10s product ad from a product photo + brand brief |
| [Product Showcase Video](library/motion/product-showcase-video/) | Dynamic product showcase with explosive ingredient arrangement + realistic motion animation |
| [Product Video Ad Maker](library/motion/product-video-ad-maker/) | High-end cinematic product video ad starting from a simple product photo |
| [Talking Baby Video](library/motion/talking-baby-video/) | Viral-style talking-baby video with custom costumes and scripts |
| [UGC Lifestyle Try-On](library/motion/ugc-lifestyle-try-on/) | UGC-style lifestyle photos & video of a person using your product — authentic, social-native |
| [UGC Video Factory](library/motion/ugc-video-factory/) | Person photo + product photo + script → 10s vertical 9:16 UGC video ad with native dialogue (Nano-Banana Pro Edit → Seedance 2.0 VIP i2v) |

**Social (5)**

| Skill | Description |
|:---|:---|
| [Instagram Post](library/social/instagram-post/) | Polished on-brand Instagram post — hero image + caption + hashtags |
| [Product Campaign Pack](library/social/product-campaign/) | Full multi-channel campaign — hero visuals, social assets, short ad video, platform crops |
| [RedNote Cover](library/social/rednote-cover/) | Xiaohongshu (小红书) cover image — vibrant lifestyle aesthetic with typography overlay |
| [Social Media Pack](library/social/social-pack/) | Re-render a hero image into Instagram / TikTok / Shorts / X aspect ratios |
| [UGC Ads Workflow](library/social/ugc-ads-workflow/) | UGC video ad pipeline — combine selfie + product image, write script, animate |

**Visual / Images & Design (21)**

| Skill | Description |
|:---|:---|
| [Action Figure Generator](library/visual/action-figure-generator/) | Convert a photo of a person into a custom 3D action figure with collectible toy packaging |
| [Ad Creative Set](library/visual/ad-creative/) | High-converting ad set — hero image, copy variations, platform crops for Meta / Google / LinkedIn |
| [Amazon Product Listing Pack](library/visual/amazon-product-listing/) | Full Amazon listing image set — hero, lifestyle, infographic, comparison/detail closeups |
| [Blog Header](library/visual/blog-header/) | Professional 1200×628 blog header image with optional title composition guidance |
| [Brand Kit](library/visual/brand-kit/) | Cohesive brand visual kit — logo concept, color palette, typography pairings |
| [Brochure Designer](library/visual/brochures/) | Multi-page brochure — cover, inner spread, back — for business, real estate, events, launches |
| [Couple Grid Creator](library/visual/couple-grid-creator/) | Stylized 6-box grid of a couple in romantic poses, each pose framed inside cardboard packaging |
| [Brand Design Guide](library/visual/design-guide/) | Comprehensive design guide — palette, typography, UI components, visual identity rules |
| [Fashion Try-On](library/visual/fashion-try-on/) | Virtually try outfits by combining a person's photo + clothing item, optional fashion model video |
| [Floor Plan Rendering](library/visual/floor-plan-rendering/) | Design a 2D floor plan and convert into a realistic 3D architectural rendering |
| [Interior Design](library/visual/interior-design/) | Pro interior design visualizations — redesign rooms, generate concepts, visualize furniture styles |
| [Interior Design Visualizer](library/visual/interior-design-visualizer/) | Generate an empty room and fill it with stylish furniture / decor; or redesign an existing room |
| [Keyboard Art Maker](library/visual/keyboard-art-maker/) | Artistic top-down photos of keyboard keycaps arranged to spell custom messages |
| [Logo + Branding Package](library/visual/logo-branding/) | Logo + full branding package — variations (dark/light/icon), palette, mockups |
| [Logo Generator](library/visual/logo-generator/) | Quick single-shot polished logo — fast, clean vector aesthetic with accurate brand-name text |
| [Multi-Angle Reshoot](library/visual/multi-angle-reshoot/) | Re-render a subject from dramatic camera angles (fish-eye, bird's-eye, low, macro) — identity preserved |
| [Multi-Angle Shots](library/visual/multi-angle-shots/) | Full multi-angle product shot set — front, side, back, top-down, 45° |
| [Selfie with Celebrities](library/visual/selfie-with-celebrities/) | Realistic behind-the-scenes selfie of the user with a celebrity; optional cinematic long-take |
| [Storyboard Generator](library/visual/storyboard/) | Generate N keyframes for a short story or scene sequence (image only, no video) |
| [URL to Design](library/visual/url-to-design/) | Analyze a website URL and generate a redesigned, improved UI with modern aesthetics |
| [YouTube Thumbnail](library/visual/youtube-thumbnail/) | High-CTR YouTube thumbnail — striking imagery, bold text placement, emotional face/subject |

Each recipe declares its `inputs` and a `Steps` body. Pass the inputs and let your agent execute the steps via `muapi` CLI calls (or raw API for endpoints that don't yet have a CLI alias — see the per-skill *Notes for the Executing Agent* footer).

---

## 🚀 Quick Start

### 1. Install the muapi CLI

The core scripts require [`muapi-cli`](https://www.npmjs.com/package/muapi-cli). Install it once:

```bash
# via npm (recommended — no Python required)
npm install -g muapi-cli

# via pip
pip install muapi-cli

# or run without installing
npx muapi-cli --help
```

### 2. Configure Your API Key

```bash
# Interactive setup
muapi auth configure

# Or pass directly
muapi auth configure --api-key "YOUR_MUAPI_KEY"

# Get your key at https://muapi.ai/dashboard
```

### 3. Install the Skills

```bash
# Install all skills to your AI agent
npx skills add SamurAIGPT/Generative-Media-Skills --all

# Or install a specific skill
npx skills add SamurAIGPT/Generative-Media-Skills --skill muapi-media-generation

# Install to specific agents
npx skills add SamurAIGPT/Generative-Media-Skills --all -a claude-code -a cursor
```

### 4. Generate Your First Image

```bash
muapi image generate "a cyberpunk city at night" --model flux-dev

# Download the result automatically
muapi image generate "a sunset over mountains" --model hidream-fast --download ./outputs

# Extract just the URL (agent-friendly)
muapi image generate "product on white bg" --model flux-schnell --output-json --jq '.outputs[0]'
```

### 5. Run an Expert Skill

```bash
# Use Nano-Banana reasoning to generate a 2K masterpiece
bash library/visual/nano-banana/scripts/generate-nano-art.sh \
  --file ./my-source-image.jpg \
  --subject "a glass hummingbird" \
  --style "macro photography" \
  --resolution "2k" \
  --view
```

### 6. Direct a Cinematic Scene

```bash
cd library/motion/cinema-director

# Create a 10-second epic reveal
bash scripts/generate-film.sh \
  --subject "a cybernetic dragon over Tokyo" \
  --intent "epic" \
  --model "kling-v3.0-pro" \
  --duration 10 \
  --view

# Animate a reference image into video
bash library/motion/seedance-2/scripts/generate-seedance.sh \
  --mode i2v \
  --file ./concept.jpg \
  --subject "camera slowly pulls back to reveal the full landscape" \
  --intent "reveal" \
  --view

# Extend an existing video
bash library/motion/seedance-2/scripts/generate-seedance.sh \
  --mode extend \
  --request-id "YOUR_REQUEST_ID" \
  --subject "camera continues pulling back to reveal the vast city" \
  --duration 10
```

---


### OpenCode

```bash
# Clone the repo and set the MUAPI_API_KEY env var
git clone https://github.com/SamurAIGPT/Generative-Media-Skills
export MUAPI_API_KEY=your_key_here

# Skills auto-load from .opencode/skills/ when you run opencode in this directory
opencode
```
## 🤖 MCP Server

Run muapi as a **Model Context Protocol server** so Claude Desktop, Cursor, or any MCP-compatible agent can call generation tools directly — no shell scripts needed.

```bash
muapi mcp serve
```

**Claude Desktop config** (`~/Library/Application Support/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "muapi": {
      "command": "muapi",
      "args": ["mcp", "serve"],
      "env": { "MUAPI_API_KEY": "your-key-here" }
    }
  }
}
```

This exposes **19 structured tools** with full JSON Schema input/output definitions:

| Tool | Description |
|------|-------------|
| `muapi_image_generate` | Text-to-image (14 models) |
| `muapi_image_edit` | Image-to-image editing (11 models) |
| `muapi_video_generate` | Text-to-video (13 models) |
| `muapi_video_from_image` | Image-to-video (16 models) |
| `muapi_audio_create` | Music generation (Suno) |
| `muapi_audio_from_text` | Sound effects (MMAudio) |
| `muapi_enhance_upscale` | AI upscaling |
| `muapi_enhance_bg_remove` | Background removal |
| `muapi_enhance_face_swap` | Face swap image/video |
| `muapi_enhance_ghibli` | Ghibli style transfer |
| `muapi_edit_lipsync` | Lip sync to audio |
| `muapi_edit_clipping` | AI highlight extraction |
| `muapi_predict_result` | Poll prediction status |
| `muapi_upload_file` | Upload local file → URL |
| `muapi_keys_list` | List API keys |
| `muapi_keys_create` | Create API key |
| `muapi_keys_delete` | Delete API key |
| `muapi_account_balance` | Get credit balance |
| `muapi_account_topup` | Add credits (Stripe checkout) |

---

## ⚡ Agentic Pipeline Examples

```bash
# Submit async, capture request_id, poll when ready
REQUEST_ID=$(muapi video generate "a dog running on a beach" \
  --model kling-master --no-wait --output-json --jq '.request_id' | tr -d '"')

# ... do other work ...

muapi predict wait "$REQUEST_ID" --download ./outputs

# Pipe a prompt from another command
generate_prompt | muapi image generate - --model flux-dev

# Chain: upload → edit → download
URL=$(muapi upload file ./photo.jpg --output-json --jq '.url' | tr -d '"')
muapi image edit "make it look like a painting" --image "$URL" \
  --model flux-kontext-pro --download ./outputs
```

---

## 📖 Schema Reference

This repository includes a streamlined `schema_data.json` that core scripts use at runtime to:
- **Validate Model IDs**: Ensures the requested model exists.
- **Resolve Endpoints**: Automatically maps model names to API endpoints.
- **Check Parameters**: Validates supported `aspect_ratio`, `resolution`, and `duration` values.

Discover all available models via the CLI:

```bash
muapi models list
muapi models list --category video --output-json
```

---

## 🔧 Compatibility

Optimized for the next generation of AI development environments:
- **Claude Code** — Direct terminal execution via tools + MCP server mode.
- **Gemini CLI / Cursor / Windsurf** — Seamless integration as local scripts.
- **MCP** — Full Model Context Protocol server with typed input/output schemas.
- **CI/CD** — `--output-json`, `--jq`, semantic exit codes for scripting.

---

## 📄 License
MIT © 2026
