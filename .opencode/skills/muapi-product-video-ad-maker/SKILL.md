---
slug: muapi-product-video-ad-maker
name: muapi-product-video-ad-maker
version: "1.0.0"
description: Create a high-end cinematic product video advertisement starting from a simple product photo.
acceptLicenseTerms: true
---


# Product Video Ad Maker

**Create a high-end cinematic product video advertisement starting from a simple product photo.**

## Inputs

| Name | Type | Required | Default | Description |
|:---|:---|:---|:---|:---|
| `product_image` | image_url | yes | — | A photo of the product to be used in the advertisement. |
| `scene_description` | text | no | surrounded by fresh flowers and soft morning sunlight | Description of the scene or background for the product. |


## Steps

### Phase A — Premium Product Rendering

If `{{product_image}}` is not provided, ask the user to upload their product photo.

Once the photo is available, submit the plan with ONE step to re-render the product in a premium setting:

1. **Product Rendering** — `muapi image edit` (model=`flux-2-pro-edit`):
   - Reference Image: `{{product_image}}`
   - Prompt: `A high-end, professional commercial photograph of the product from the reference image, {{scene_description}}. Soft studio lighting, realistic reflections, cinematic depth of field, sharp focus on the product. 8k resolution, elegant and minimal composition.`
   - Aspect ratio: 1:1 or 4:5

Present the premium product image to the user for approval.

### Phase B — Cinematic Video Ad Generation

Once the image is approved, submit the plan to animate it into a video ad:

1. **Video Ad Generation** — `muapi video from-image` (model=`wan2.5-image-to-video-fast`):
   - Reference Image: The premium image from Phase A.
   - Prompt: `A cinematic product advertisement video. Smooth, slow-motion camera movement panning across the product. Subtle environmental movements (e.g., leaves swaying, light shifting). High-quality commercial cinematography, elegant transitions, professional look.`
   - Aspect ratio: 16:9 or 9:16

After generation, present the final product video advertisement to the user.

## Trigger Keywords

`product video ad`, `video ad maker`, `cinematic product video`, `commercial video maker`, `professional product ad`


---

## Notes for the Executing Agent

- This recipe is LLM-orchestrated: read each phase, gather any missing inputs from the user, then call `muapi` CLI commands. Use `muapi auth configure` first if `MUAPI_API_KEY` is unset.
- For model IDs without a CLI alias yet, fall back to the raw endpoint via `curl -X POST https://api.muapi.ai/api/v1/<endpoint> -H "x-api-key: $MUAPI_API_KEY" -H 'content-type: application/json' -d '{...}'` and poll with `muapi predict wait <request_id>`.
- Substitute `{{input_name}}` placeholders with the user's actual inputs before issuing each call.
