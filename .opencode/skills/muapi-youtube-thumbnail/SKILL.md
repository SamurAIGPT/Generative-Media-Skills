---
slug: muapi-youtube-thumbnail
name: muapi-youtube-thumbnail
version: "1.0.0"
description: Design a high-CTR YouTube thumbnail — striking imagery, bold text placement, and emotional face/subject if needed.
acceptLicenseTerms: true
---


# YouTube Thumbnail

**Design a high-CTR YouTube thumbnail — striking imagery, bold text placement, and emotional face/subject if needed.**

## Inputs

| Name | Type | Required | Default | Description |
|:---|:---|:---|:---|:---|
| `title` | text | yes | — | The video title or topic (e.g. "I tried 7 AI tools in 24 hours — here's what happened"). |
| `channel_style` | text | no | bold, high contrast, bright colors, clean design, YouTube tech aesthetic | Channel brand style (e.g. "dark moody gaming", "bright educational", "minimal corporate"). |
| `subject_description` | text | no | — | Optional description of the person or subject to feature (e.g. "a surprised young man in a hoodie"). |


## Steps

Thumbnails are the #1 factor in YouTube CTR. Generate a single, maximum-impact 16:9 image.

### Phase A — Plan the composition

Before generating, briefly reason about the best thumbnail formula for this topic:
- **Emotion-first**: shocked/curious face if relevant + bold text = high CTR
- **Text overlay**: 3–5 words max, high-contrast (white/yellow on dark, or vice-versa)
- **Contrast & saturation**: thumbnails compete in a grid — they must pop

### Phase B — Generate the thumbnail

1. Build the image generation prompt:
   - Subject: `{{subject_description}}` if provided, otherwise design an object/scene that dramatizes the topic.
   - Mood: derives from `{{channel_style}}`.
   - Composition: rule-of-thirds, subject on left or right with empty space for text.
   - Style tags: `{{channel_style}}, youtube thumbnail composition, ultra detailed, vibrant, high contrast, 16:9`.
2. Call `muapi image generate` (model=gpt-image-2-text-to-image, aspect_ratio=16:9).

### Phase C — Text overlay guidance

After generation, return:
- **Suggested overlay text**: 3–5 bold words that complement the title `{{title}}`.
- **Text placement**: where on the canvas to position text (e.g. "bold yellow text, top-right third").
- **Font recommendation**: style suggestion (e.g. "Impact-style all-caps with black outline").

## Notes
- Never put too much text in the prompt — text rendering in image models is unreliable. Guide the user on adding text in post-production (Canva, Photoshop).
- If the user already has a channel image or face photo in the session, use `muapi image edit` to incorporate it.
- Suggest A/B variants only if the user asks.

## Trigger Keywords

`youtube thumbnail`, `yt thumbnail`, `thumbnail`, `video thumbnail`, `youtube cover`


---

## Notes for the Executing Agent

- This recipe is LLM-orchestrated: read each phase, gather any missing inputs from the user, then call `muapi` CLI commands. Use `muapi auth configure` first if `MUAPI_API_KEY` is unset.
- For model IDs without a CLI alias yet, fall back to the raw endpoint via `curl -X POST https://api.muapi.ai/api/v1/<endpoint> -H "x-api-key: $MUAPI_API_KEY" -H 'content-type: application/json' -d '{...}'` and poll with `muapi predict wait <request_id>`.
- Substitute `{{input_name}}` placeholders with the user's actual inputs before issuing each call.
