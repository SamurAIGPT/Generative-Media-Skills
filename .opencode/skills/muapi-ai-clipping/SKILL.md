---
slug: muapi-ai-clipping
name: muapi-ai-clipping
version: "1.0.0"
description: Turn a long video into N viral-ready short clips with a single managed API call. Wraps muapi.ai's `/ai-clipping` endpoint, which handles transcription, highlight ranking through a virality framework (hook / emotional peak / opinion bomb / revelation / conflict / quotable / story peak / practical value), overlap dedupe, and vertical face-tracking auto-crop server-side. No local Whisper, no local LLM, no GPU.
acceptLicenseTerms: true
---

# AI Clipping

**One API call: long video in → ranked vertical short clips out.**

Each clip ships with a viral score (0–100), an opening hook line, a one-sentence "why it works" reason, and a hosted mp4 URL.

Underlying API: https://muapi.ai/playground/ai-clipping
Reference implementation (open source): https://github.com/SamurAIGPT/AI-Youtube-Shorts-Generator

---

## When to Use

- Auto-clip a podcast, interview, lecture, vlog, or stream into TikTok / Reels / Shorts.
- Extract the best 30–75s moments from any hosted video URL.
- Get face-tracked vertical (9:16), square (1:1), or portrait (4:5) crops without running ffmpeg locally.

If you only need raw timestamps for your own renderer, set `--coords-only` to skip cropping and just get the highlight ranges.

---

## Agent Execution Protocol

### Step 1 — Collect Inputs

| Input | Required | Default | Notes |
|:---|:---|:---|:---|
| `--video` | yes | — | Hosted mp4 URL, or local file path (auto-uploaded), or YouTube URL (if backend supports it) |
| `--num-clips` | no | `3` | Number of highlights to extract |
| `--aspect-ratio` | no | `9:16` | `9:16` \| `1:1` \| `4:5` |
| `--coords-only` | no | off | Return just the highlight time ranges, skip cropping |

If the user gave only a video URL, run with defaults — don't block on questions.

---

### Step 2 — Verify Prerequisites

- `muapi-cli` installed and authed (`muapi auth configure`)
- `MUAPI_API_KEY` available (env var or `muapi auth status` passes)

That's it. No `ffmpeg`, no Python, no Whisper install, no LLM keys. Everything runs server-side.

---

### Step 3 — Run the Skill

```bash
bash library/edit/ai-clipping/scripts/run-ai-clipping.sh \
  --video "https://example.com/podcast.mp4" \
  --num-clips 5 \
  --aspect-ratio 9:16 \
  --view
```

The script:
1. Resolves `--video` to a hosted URL (uploads local files via `muapi upload file` if needed).
2. Calls `muapi edit clipping` with the supported parameters.
3. Polls until the job is done (or returns the `request_id` immediately under `--async`).
4. Prints a ranked summary and, if `--output-json` is set, writes the full result.

---

## What Happens Server-Side

The `/ai-clipping` endpoint internally runs the full pipeline so the agent doesn't have to:

- **Transcribe** with Whisper.
- **Classify content type** (podcast / interview / tutorial / vlog / lecture / monologue).
- **Rank highlights** through the virality framework:
  - **Hook moments** — strong opening line that stops the scroll
  - **Emotional peaks** — laughter, anger, vulnerability, awe
  - **Opinion bombs** — spicy, contrarian, debate-bait takes
  - **Revelation moments** — "wait, what?" reframes
  - **Conflict** — disagreement, tension, callouts
  - **Quotable lines** — tight, screenshot-worthy phrasing
  - **Story peaks** — climax of a narrative arc
  - **Practical value** — actionable insight a viewer will save
- **Dedupe** overlapping candidates by score.
- **Top-N select** and **face-track auto-crop** to the requested aspect ratio.

This is why the skill is small: the heavy lifting is on the API.

---

## Quick Invocation Patterns

**Defaults — three 9:16 clips:**
```bash
bash run-ai-clipping.sh --video "https://example.com/long.mp4"
```

**Podcast — more clips, view in player:**
```bash
bash run-ai-clipping.sh --video "<URL>" --num-clips 8 --view
```

**Square clips for Instagram feed:**
```bash
bash run-ai-clipping.sh --video "<URL>" --aspect-ratio 1:1 --num-clips 3
```

**Just the timestamps (build your own renderer):**
```bash
bash run-ai-clipping.sh --video "<URL>" --coords-only --output-json result.json
```

**Async submit (returns request_id, poll later):**
```bash
REQUEST_ID=$(bash run-ai-clipping.sh --video "<URL>" --async --output-json - | jq -r '.request_id')
muapi predict wait "$REQUEST_ID" --download ./outputs
```

**Local file:**
```bash
bash run-ai-clipping.sh --video ./recording.mp4 --num-clips 5 --view
```

**Batch — `urls.txt` with one URL per line:**
```bash
xargs -a urls.txt -I{} bash run-ai-clipping.sh --video "{}"
```

---

## Aspect Ratio Picker

| Platform | Ratio | Sweet-spot duration |
|:---|:---|:---|
| TikTok / Reels / YouTube Shorts | `9:16` | 30–75s |
| Instagram Feed | `1:1` | 15–45s |
| Pinterest / portrait | `4:5` | 30–60s |

Default to `9:16` unless the platform is specified.

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

When `--coords-only` is set, each entry has `start_time`/`end_time` but no `clip_url` — render locally with ffmpeg.

When reporting back to the user, surface for each clip: rank, score, time range, title, hook, and clip URL.

---

## Common Mistakes to Avoid

1. **Wrong aspect ratio for the platform** — Shorts / TikTok / Reels are `9:16`. Default to that.
2. **Padding to hit `num_clips`** — if the API returns fewer survivors than requested, return what you have. Don't pretend.
3. **Re-running on a 404'd clip URL** — the same `request_id` can be re-fetched with `muapi predict wait <id>` rather than re-clipping.
4. **Trying to tune Whisper / chunk size / LLM prompts** — those knobs aren't exposed; the endpoint handles them.

---

## Failure Modes

- **API key missing or rejected** — surface the exact error; never fabricate a key.
- **Job timed out** — bump poll timeout (`--poll-timeout`) and retry.
- **Source URL not reachable from the backend** — upload locally with `muapi upload file <path>` first, then pass the returned URL.
- **Fewer clips returned than requested** — the source had fewer rankable highlights. Return what came back with a note.

---

## Done Criteria

The skill is done when:
1. `result.shorts` has up to `num_clips` entries, each with a working `clip_url` (or `start_time`/`end_time` under `--coords-only`).
2. The user has been shown the ranked list (score, time range, title, hook, URL).
3. If `--output-json` was set, the file exists and parses.
