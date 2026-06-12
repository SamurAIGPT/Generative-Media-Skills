---
slug: muapi-instagram-post
name: muapi-instagram-post
version: "1.0.0"
description: Create a polished, on-brand Instagram post — square or portrait hero image with matching caption and hashtags.
acceptLicenseTerms: true
---


# Instagram Post

**Create a polished, on-brand Instagram post — square or portrait hero image with matching caption and hashtags.**

## Inputs

| Name | Type | Required | Default | Description |
|:---|:---|:---|:---|:---|
| `brief` | text | yes | — | What the post is about (e.g. "summer coffee launch at our café, warm golden vibes"). |
| `brand_style` | text | no | modern, vibrant, clean typography, lifestyle photography aesthetic | Brand personality and visual style tags. |
| `format` | text | no | 1:1 | Post format — "1:1" for feed square, "4:5" for portrait feed, "9:16" for Reels. |


## Steps

This skill produces one polished Instagram-ready visual + caption. Use the plan if generating more than one variant.

### Phase A — Generate the hero image

1. Write a detailed, atmosphere-rich image prompt based on `{{brief}}` and `{{brand_style}}`:
   - Include lighting direction, color palette, mood, subject placement, and lens feel.
   - Optimize for Instagram aesthetics: clean, punchy, single focal point.
   - Append style tags: `{{brand_style}}, social media photography, highly detailed`.
2. Call `muapi image generate` (model=nano-banana-2, aspect_ratio=`{{format}}`).
3. If the user provided a product or subject image in the session, prefer `muapi image edit` instead to maintain visual consistency.

### Phase B — Caption & Hashtags

After the image is generated, compose and return:
- **Caption**: 2–4 lines. Hook line first (punchy, curiosity-driving), then brand message, then CTA.
- **Hashtags**: 15–20 targeted hashtags (mix of niche, mid-tier, and broad). Format as a separate block.

## Notes
- Prioritize scroll-stopping first impressions — the image must visually communicate the brief within 2 seconds.
- If `format` is `9:16` (Reels cover), note that text overlays are common; include a suggestion for on-screen text placement.
- Do NOT generate multiple variants unless the user explicitly asks.

## Trigger Keywords

`instagram post`, `ig post`, `instagram`, `feed post`, `instagram creative`


---

## Notes for the Executing Agent

- This recipe is LLM-orchestrated: read each phase, gather any missing inputs from the user, then call `muapi` CLI commands. Use `muapi auth configure` first if `MUAPI_API_KEY` is unset.
- For model IDs without a CLI alias yet, fall back to the raw endpoint via `curl -X POST https://api.muapi.ai/api/v1/<endpoint> -H "x-api-key: $MUAPI_API_KEY" -H 'content-type: application/json' -d '{...}'` and poll with `muapi predict wait <request_id>`.
- Substitute `{{input_name}}` placeholders with the user's actual inputs before issuing each call.
