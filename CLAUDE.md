# CLAUDE.md — operating contract for ai-video-flow

You are running the **ai-video-flow** production system: turn any SKU into on-brand video on demand, repeatably, with measured unit economics. Read this file every session before acting.

## Golden rules (never violate)

1. **Never auto-publish** to a live brand channel. You may generate, QA, assemble, and export end-to-end. The post to a live channel is always a human decision — stop and hand off.
2. **No real-celebrity likeness.** Original characters only. The platform's pre-prompt face filter is built to flag real-person likeness; don't fight it.
3. **Generic branding only.** Original crest/sponsor marks; never reproduce real trademarked kit branding.
4. **Budget cap: 3 re-rolls per shot, then stop.** Preflight cost before batching. A runaway shot eats margin.
5. **Preflight before you spend.** Call `generate_video` with `get_cost: true` first to read the real credit cost without submitting a job. Log it.

## Default generation params

- **Model:** `seedance_2_0` (ByteDance). Reference-driven, identity-consistent, multi-SKU. Duration 4–15s. Aspect ratios incl. `9:16`, `1:1`, `16:9`.
- **Defaults:** `resolution: 720p`, `mode: std` (use `fast` for cheaper drafts; `fast` does not support 1080p), `aspect_ratio: 9:16` unless the brief says otherwise.
- **Commercial/product ads:** prefer `marketing_studio_video` (one-click product ads with `product_ids`, hooks, settings, and `ad_reference_id` to recreate a reference ad). This is the SKU-scale surface.
- **Model field is a HINT, not a hard constraint.** The platform may reroute if a prompt is off-brief. After every generation, verify the result matches the intended model/brief — this is part of the QA gate, not an afterthought.

## Identity architecture (read carefully — this is a common trap)

A trained **Soul** is usable **only** with `text2image_soul_v2` and `soul_cinema_studio` — **NOT** with Seedance 2.0. Two valid paths:

- **Path A — Element (default for Seedance):** create a reusable character via `show_reference_elements` (action=create) from one or more images. Drive Seedance by embedding the placeholder `<<<element_id>>>` directly inside `params.prompt`; the backend auto-injects the reference. Elements work with `seedance_2_0`, `kling3_0`, Cinema Studio video, Nano Banana, Seedream, etc.
- **Path B — Soul → Seedance (highest identity fidelity):** train a Soul (`show_characters` action=train, **5–20 reference images**, ~10 min) → generate identity-locked stills with Soul V2 → feed those stills into Seedance as `medias` with role `start_image`. Soul owns the face; Seedance owns the motion. Soul V2/Cinema gens are near-free on a paid plan, so spend real credits only on the Seedance video.

## Toolmap (Higgsfield MCP — verified names)

| Need | Tool |
|---|---|
| Generate video | `generate_video` (params: `model`, `prompt`, `aspect_ratio`, `duration`, `count`, `medias[]`, `get_cost`) |
| Generate image / Soul still | `generate_image` (model `soul_2` / `soul_cinema_studio` for Soul) |
| Preflight cost | `generate_video` with `get_cost: true` |
| Inspect a job | `job_display` (by job UUID) |
| Upload a local file | `media_upload` → PUT bytes → `media_confirm` (returns media_id UUID) |
| Reusable character/env/prop | `show_reference_elements` (Element, for Seedance) |
| Train reusable identity | `show_characters` action=train (Soul, 5–20 imgs, Soul V2/Cinema only) |
| Find / verify models | `models_explore` (list/search/get/recommend) |
| Product ads at SKU scale | `marketing_studio_video` + `show_marketing_studio` |
| Predict performance | `virality_predictor` |
| Polish | `upscale_video`, `reframe` |
| Billing / credits | `show_plans_and_credits`, `balance` |

Media `value` accepts: a `media_id` UUID (from `media_confirm`), a prior generation's `job_id`, or an `https://` URL. Never pass local file paths to generation — upload first.

## How to run

`/make-campaign <SKU>` executes the full pipeline (see `.claude/commands/make-campaign.md`). The operational playbook, QA rubric, and decision tree live in `pipeline.md`. The shot templates live in `prompts.md`. Always log to `qa-log.csv` and roll up to `metrics.csv`.

## Credit discipline

Credits come only via subscription (Plus $49/mo = 1,000 credits at time of writing; no à-la-carte packs on individual workspaces). Credits don't roll over. Preflight cost, respect the re-roll cap, and record actual credits-per-clip in the logs so the cost-per-usable number is real, not estimated.
