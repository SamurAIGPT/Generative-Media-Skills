---
slug: muapi-ugc-video-factory
name: muapi-ugc-video-factory
version: "1.0.0"
description: Turn a person photo + a product photo + an optional script into a vertical 9:16 UGC-style video ad. Generates a lifestyle hero image (Nano-Banana Pro Edit), then animates it with native audio using Seedance 2.0 VIP image-to-video.
acceptLicenseTerms: true
---


# UGC Video Factory

**Turn a person photo + product photo (+ optional script & environment) into a vertical 9:16 UGC-style video ad with native dialogue audio.**

A three-stage pipeline:
1. **GPT** writes a director-grade ultra-realistic lifestyle photography prompt from your inputs.
2. **Nano-Banana Pro Edit** fuses the person + product into a single hero photo (1K, 9:16).
3. **Seedance 2.0 VIP Image-to-Video** animates the hero photo into a 10s vertical UGC clip with synced spoken audio.

## Inputs

| Name | Type | Required | Default | Description |
|:---|:---|:---|:---|:---|
| `person` | image_url | yes | — | Photo of the person who will appear in the ad (face + upper body works best). |
| `product` | image_url | yes | — | Clear photo of the product (preferably on neutral background, logo/text legible). |
| `script` | text | no | `Okay… first of all, ship happens. And this hat is honestly my favorite. It also comes in navy and black, so you can pick your vibe.` | The exact line the on-screen person will say (kept short — 1–2 sentences fit 10s comfortably). |
| `environment` | text | no | `study room, laptop in front of it` | Scene / context where the person is using the product (e.g. "bathroom mirror, morning routine", "coffee shop window seat"). |

If `person` or `product` is missing, ask the user to upload them (`muapi upload file <path>`) or offer to generate placeholders before continuing.


## Steps

Run the three steps sequentially — each step's output feeds the next.

### Step 1 — Director Prompt (GPT)

Use a GPT model (`gpt-5.1` or whichever chat model is available to the executing agent) with **temperature 0** and **max ~200 tokens** to produce the hero-image prompt.

**System prompt:** `You are a helpful assistant.`

**User prompt** (substitute `{{person}}`, `{{product}}`, `{{environment}}`):

```
Uploaded images are being analyzed. Ultra-realistic lifestyle photography with {{person}} and {{product}} and {{environment}}.

If the product is wearable (e.g., hat, glasses, hooded sweatshirt), the person wears the product naturally.

If the product is carried in the hand (e.g., cream, bottle, thermos), the person holds the product naturally.

The product is clearly visible and is the main focus of the image. The logo or text on the product must be legible.

The person has a natural and modern look with a minimalist style.

The scene is consistent with the context of the product's use: {{environment}}.

Lighting: soft natural daylight.
Background: clean, aesthetic, slightly blurred (shallow depth of field).
Style: high-end commercial lifestyle photography, realistic textures, 4K quality, vertical 9:16 composition, social-media advertising style. The background and environment should be appropriate to the product (e.g. a woman with a serum could be at home). The person's facial details and the product must remain unchanged.
```

Capture the GPT response as `{{step1_prompt}}`.

### Step 2 — Hero Image (Nano-Banana Pro Edit)

Submit a `muapi image edit` call against the `nano-banana-pro-edit` model:

- **Reference images** (`image_urls`): `[ {{person}}, {{product}} ]` — order matters; person first.
- **Prompt**: `{{step1_prompt}}` from Step 1.
- **Aspect ratio**: `9:16`
- **Num images**: `1`
- **Resolution**: `1K`
- **Output format**: `jpeg`

Capture the resulting image URL as `{{hero_image}}`. Briefly show it to the user for approval before kicking off the video step.

### Step 3 — UGC Video (Seedance 2.0 VIP Image-to-Video)

Submit a `muapi video from-image` call against **`seedance-2-vip-image-to-video`** (or the `-fast` variant if the executing agent wants lower latency).

- **Start image**: `{{hero_image}}` from Step 2.
- **Aspect ratio**: `9:16`
- **Duration**: `10` seconds.
- **Generate audio**: `true` (native dialogue).
- **CFG scale**: `0.5`
- **Negative prompt**: `blur, distort, low quality`
- **Prompt** (substitute `{{script}}`):

```
Create a 10-second vertical UGC-style video (9:16).

A person is interacting naturally with their setting and product.

The product is used naturally:
- If wearable → the person is wearing it.
- If handheld → the person is holding or applying it.

The video is a single, uninterrupted shot. No cuts. No color changes. No text on screen.

The person looks directly at the camera with a relaxed and natural expression.
They interact comfortably with the product using their hands (adjusting, holding, pointing).

They say in a natural, conversational tone:

"{{script}}"

Subtle hand gestures while speaking.
End with a small smile or nod.

Style: authentic UGC, handheld phone feel, light natural movement, soft daylight, shallow depth of field, TikTok/Reels aesthetic.
```

Poll the result with `muapi predict wait <request_id>` and download to the user's outputs directory.

## Notes

- VIP tier supports 9:16 and durations 4–15s; 10s is the sweet spot for a 1–2 sentence script.
- Keep the script short — Seedance 2.0 will compress longer scripts and clip words.
- Seedance VIP tolerates realistic human faces in references (unlike Chinese tier), making it the right choice for UGC.
- If you want lower latency at the same quality, swap to `seedance-2-vip-image-to-video-fast`.
- For multi-shot ads, generate several `{{hero_image}}` variations in Step 2 and animate each independently — Seedance VIP does not multi-image i2v at 9:16 + audio.

## Trigger Keywords

`ugc video factory`, `ugc video ad`, `person plus product video`, `talking product ad`, `ugc reel`, `lifestyle product video`, `vertical ugc video`


---

## Notes for the Executing Agent

- This recipe is LLM-orchestrated: read each phase, gather any missing inputs from the user, then call `muapi` CLI commands. Run `muapi auth configure` first if `MUAPI_API_KEY` is unset.
- For local files supplied by the user, upload them first: `muapi upload file <path> --output-json --jq '.url'`.
- Substitute `{{input_name}}` placeholders with the user's actual inputs before issuing each call.
- If the `muapi` CLI does not yet alias `nano-banana-pro-edit` or `seedance-2-vip-image-to-video`, fall back to the raw API: `curl -X POST https://api.muapi.ai/api/v1/<endpoint> -H "x-api-key: $MUAPI_API_KEY" -H 'content-type: application/json' -d '{...}'`, then poll with `muapi predict wait <request_id>`.
