---
slug: muapi-ad-creative
name: muapi-ad-creative
version: "1.0.0"
description: Generate a high-converting ad creative set — hero image, ad copy variations, and platform-optimized crops for Meta, Google Display, and LinkedIn.
acceptLicenseTerms: true
---


# Ad Creative Set

**Generate a high-converting ad creative set — hero image, ad copy variations, and platform-optimized crops for Meta, Google Display, and LinkedIn.**

## Inputs

| Name | Type | Required | Default | Description |
|:---|:---|:---|:---|:---|
| `product_or_service` | text | yes | — | What is being advertised (e.g. "SaaS project management tool for remote teams"). |
| `target_audience` | text | yes | — | Who the ad is for (e.g. "startup founders aged 25–40, tech-savvy"). |
| `campaign_goal` | text | no | awareness | Campaign objective — "awareness", "consideration", or "conversion". |
| `tone` | text | no | professional, clean, modern | Creative tone and visual style (e.g. "bold and disruptive", "luxury minimal", "friendly and approachable"). |
| `product_image` | image_url | no | — | Optional product or brand image URL already in the session. |


## Steps

This skill has TWO phases. Phase A creates the hero concept for approval; Phase B fans out to platform formats.

### Phase A — Hero image + Ad copy

Submit ONE the plan with:

1. **Hero image** — `muapi image generate` (model=nano-banana-pro) or `muapi image edit` (model=nano-banana-pro-edit) if `{{product_image}}` is provided:
   - Aspect ratio: 1:1 (universal starting point).
   - Prompt must capture: product/service benefit, target audience lifestyle cue, campaign tone.
   - Style: `{{tone}}, advertising photography, clean background, product focus, ultra detailed, commercial quality`.
   - Tier: quality.

After the plan executes, present the hero asset and 3 ad copy variations:
- **Variation A** — Problem-aware hook: "Tired of X? [Product] fixes that."
- **Variation B** — Benefit-led: "[Feature] → [Outcome] for [Audience]."
- **Variation C** — Social proof / urgency: "X teams already use [Product]."
Each variation includes: Headline (6 words max), Body (20–30 words), CTA button text.

Ask which copy variation to use for Phase B. Wait for user confirmation.

### Phase B — Platform crops

Once the user picks a copy direction, submit a SECOND the plan with parallel crops:

1. `muapi image edit` → 1:1 (Facebook/Instagram feed, 1080×1080)
2. `muapi image edit` → 9:16 (Story/Reels, 1080×1920)
3. `muapi image edit` → 1.91:1 (Facebook feed wide, 1200×628)
4. `muapi image edit` → 1:1 (LinkedIn feed, same as FB)

For each crop:
- Prompt: "Reframe for [platform] ad format. Keep product/subject centered and uncropped. Maintain original palette and tone. Leave headroom/footroom for text overlays."
- All crops run in parallel.

Return one asset per format with the recommended copy overlay placement for each.

## Notes
- If `campaign_goal` is "conversion", emphasize urgency and direct CTA in copy.
- If `campaign_goal` is "awareness", prioritize visual impact over text density.
- Reference `product_image` via `$nX.url` syntax in Phase B nodes to ensure consistency.
- Do NOT auto-confirm Phase B without user picking a copy variation.

## Trigger Keywords

`ad creative`, `advertisement`, `facebook ad`, `meta ad`, `google ad`, `linkedin ad`, `paid ad`, `ad banner`, `display ad`


---

## Notes for the Executing Agent

- This recipe is LLM-orchestrated: read each phase, gather any missing inputs from the user, then call `muapi` CLI commands. Use `muapi auth configure` first if `MUAPI_API_KEY` is unset.
- For model IDs without a CLI alias yet, fall back to the raw endpoint via `curl -X POST https://api.muapi.ai/api/v1/<endpoint> -H "x-api-key: $MUAPI_API_KEY" -H 'content-type: application/json' -d '{...}'` and poll with `muapi predict wait <request_id>`.
- Substitute `{{input_name}}` placeholders with the user's actual inputs before issuing each call.
