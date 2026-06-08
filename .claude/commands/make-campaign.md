---
description: Run a full AI video campaign for one SKU — batch generate, QA, log yield + cost.
argument-hint: <SKU-name> [shot-count]
allowed-tools: Read, Edit, Write, mcp__*__generate_video, mcp__*__job_display, mcp__*__show_reference_elements, mcp__*__show_characters, mcp__*__media_upload, mcp__*__media_confirm, mcp__*__models_explore, mcp__*__balance
---

# /make-campaign — one command, one campaign

Run the ai-video-flow pipeline end to end for SKU: **$1** (shot count: **$2**, default 5).

First read `CLAUDE.md`, `pipeline.md`, and `prompts.md` so you operate by the rules and templates. Then execute:

## 1. Brief
Confirm the one-page spec for **$1**: objective, channel, output format (default `9:16`), do-not-show list, shot count ($2 or 5), and the budget (**3 re-rolls/shot cap**). If anything essential is missing, ask once, then proceed.

## 2. Reference pack
Resolve the character reference:
- If a reusable **Element** or **Soul** for this campaign already exists, use it (`show_reference_elements` list / `show_characters` list).
- Otherwise create one. For Seedance, prefer an **Element** (`show_reference_elements` action=create). For highest identity fidelity, train a **Soul** (5–20 images) and generate a start-frame still with Soul V2.
- Upload any local refs via `media_upload` → PUT → `media_confirm`; never pass local paths to generation.

## 3. Shot list
Instantiate the 5-shot coverage template from `prompts.md`, filling `{product}/{subject}/{setting}/{move}/{grade}` for **$1**. Embed the Element placeholder `<<<element_id>>>` in each tag line, or attach the Soul start-frame as a `start_image` media.

## 4. Generate (preflight first)
For each shot:
1. **Preflight cost:** call `generate_video` with `get_cost: true` and the full params. Record the credit cost. Sum the batch; if it exceeds the campaign budget, stop and report.
2. Submit `generate_video` (`model: seedance_2_0`, `aspect_ratio`, `duration`, `resolution: 720p`, `mode: std`). Use `count` up to 4 for variants.
3. Poll each job with `job_display` until `completed / failed / nsfw`.

## 5. QA gate
Score every completed clip on the 5 dimensions from `pipeline.md` (identity hold, garment fidelity, motion plausibility, prompt adherence incl. model-routing check, channel fitness). Apply the decision tree:
- **hard-fail** → discard, do not re-roll the same setup.
- **soft-fail** → re-gen with reinforced reference weighting (fix the ref pack, not the wording). **Cap 3 re-rolls per shot.**
- **pass** → keep the best take.

## 6. Log
Append one row per generation (including re-rolls) to `qa-log.csv` with timestamp, campaign, sku, shot_id, shot_name, job_id, model, reroll_count, the five scores, verdict, credits, notes.

## 7. Report
Print a summary: usable/total (**yield rate**), total credits spent, **cost-per-usable** in $, turnaround, and which shots passed. Append the roll-up to `metrics.csv`.

## 8. Stop at the gate
Assemble/export is fine. **Do NOT publish to any live channel** — hand the exported clips to the human for the publish decision.
