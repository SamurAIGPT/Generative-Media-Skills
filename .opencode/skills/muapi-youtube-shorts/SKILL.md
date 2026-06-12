---
slug: muapi-youtube-shorts
name: muapi-youtube-shorts
version: "2.0.0"
description: Auto-generate viral 9:16 YouTube Shorts (or TikTok / Reels clips) from a long-form video. Thin platform-aware wrapper around the AI Clipping skill — picks sensible defaults for short-form social platforms (9:16, 30–60s sweet spot) and delegates the actual highlight extraction + crop to muapi.ai's `/ai-clipping` endpoint.
acceptLicenseTerms: true
---

# YouTube Shorts Generator

**Long video → ranked vertical short clips, tuned for short-form social.**

This skill is a platform-aware preset over the [AI Clipping](../../edit/ai-clipping/) primitive. It picks the right aspect ratio and clip count for the target platform and delegates highlight extraction, dedupe, and face-tracked auto-crop to muapi.ai's managed `/ai-clipping` endpoint.

Reference implementation: https://github.com/SamurAIGPT/AI-Youtube-Shorts-Generator
Underlying API: https://muapi.ai/playground/ai-clipping

---

## When to Use This vs. AI Clipping

| Use this skill when… | Use [AI Clipping](../../edit/ai-clipping/) directly when… |
|:---|:---|
| Target is YouTube Shorts / TikTok / Reels | You want full control over aspect / count |
| You want platform-tuned defaults | You want raw timestamps (`--coords-only`) |
| You'd rather pass `--platform tiktok` than think about ratios | You're integrating into a custom renderer |

---

## Agent Execution Protocol

### Step 1 — Collect Inputs

| Input | Default | Notes |
|:---|:---|:---|
| `--source` | — | YouTube URL, hosted mp4 URL, or local file |
| `--platform` | `shorts` | `shorts` \| `tiktok` \| `reels` \| `feed` (sets ratio + count defaults) |
| `--num-clips` | platform default | Override clip count |
| `--aspect-ratio` | platform default | Override aspect ratio |

If the user gave only a URL, run with platform defaults — don't block.

---

### Step 2 — Verify Prerequisites

- `muapi-cli` installed and authed (`muapi auth configure`)
- `MUAPI_API_KEY` available

That's it. Transcription, highlight ranking, dedupe, and cropping all run server-side — no `ffmpeg`, no Python, no Whisper, no LLM keys needed locally.

---

### Step 3 — Run the Pipeline

```bash
bash library/social/youtube-shorts/scripts/run-youtube-shorts.sh \
  --source "<YOUTUBE_URL>" \
  --platform shorts \
  --num-clips 5 \
  --view
```

The script:
1. Resolves the source (uploads local files to muapi CDN if needed).
2. Picks platform defaults if `--aspect-ratio` / `--num-clips` aren't passed.
3. Calls `muapi edit clipping` (the `/ai-clipping` endpoint) with the chosen params.
4. Polls until done, prints a ranked summary, optionally downloads / opens clips.

---

## What Happens Server-Side

The `/ai-clipping` endpoint runs the full pipeline:

- **Transcribes** the audio.
- **Ranks highlights** through a virality framework — hook moments, emotional peaks, opinion bombs, revelation moments, conflict, quotable lines, story peaks, practical value.
- **Dedupes** overlapping candidates by score.
- **Top-N selects** and **face-tracks** vertical crops.

Each clip ships with score (0–100), opening hook line, and a one-sentence "why it works" reason.

---

## Platform Defaults

| Platform | Flag | Aspect | Default clips | Notes |
|:---|:---|:---|:---|:---|
| YouTube Shorts | `--platform shorts` | `9:16` | 3 | Hook in first 1s |
| TikTok | `--platform tiktok` | `9:16` | 5 | Higher energy, longer ok |
| Instagram Reels | `--platform reels` | `9:16` | 3 | Hook in first 1s |
| Instagram Feed | `--platform feed` | `1:1` | 3 | Static-feel works well |

Override any default with `--aspect-ratio` / `--num-clips`.

---

## Quick Invocation Patterns

**Single video, defaults:**
```bash
bash run-youtube-shorts.sh --source "https://youtube.com/watch?v=VIDEO_ID"
```

**TikTok preset — 5 clips, view in player:**
```bash
bash run-youtube-shorts.sh --source "<URL>" --platform tiktok --view
```

**Square Instagram feed clips:**
```bash
bash run-youtube-shorts.sh --source "<URL>" --platform feed --num-clips 3
```

**Batch — `urls.txt` with one URL per line:**
```bash
xargs -a urls.txt -I{} bash run-youtube-shorts.sh --source "{}"
```

**Async submit (returns request_id, poll later):**
```bash
REQUEST_ID=$(bash run-youtube-shorts.sh --source "<URL>" --async --output-json - | jq -r '.request_id')
muapi predict wait "$REQUEST_ID" --download ./outputs
```

---

## Output Schema

```json
{
  "source_video_url": "...",
  "shorts": [
    {
      "title": "The one mistake that cost me $50K",
      "start_time": 124.3,
      "end_time": 187.6,
      "score": 92,
      "hook_sentence": "Nobody talks about this, but it killed my first startup...",
      "virality_reason": "Opens with a number + regret, peaks on a contrarian lesson",
      "clip_url": "https://.../short_1.mp4"
    }
  ]
}
```

When reporting back, surface for each clip: rank, score, time range, title, hook, and clip URL.

---

## Common Mistakes to Avoid

1. **Wrong aspect ratio for the platform** — Shorts / TikTok / Reels are `9:16`. The platform preset handles this; only override if you know why.
2. **Padding to hit `--num-clips`** — if the API returns fewer survivors, return what you have. Don't ship low-score filler.
3. **Re-running on a 404'd clip URL** — re-fetch the same `request_id` with `muapi predict wait <id>` rather than re-clipping.

---

## Failure Modes

- **API key missing or rejected** — surface the error; don't fabricate a key.
- **Job timed out** — bump `--poll-timeout` and retry.
- **Source URL not reachable** — upload the file via `muapi upload file` and pass the returned URL.
- **Fewer clips returned than requested** — source had fewer rankable highlights. Return what came back with a note.

---

## Done Criteria

The skill is done when:
1. `result.shorts` has up to `num_clips` entries, each with a working `clip_url`.
2. The user has been shown the ranked list (score, time range, title, hook, URL).
3. If `--output-json` was set, the file exists and parses.
