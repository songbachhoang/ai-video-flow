# pipeline.md — the 8-stage playbook

One quality gate. One feedback loop. Eight stages. The budget is set in stage 1, before a single credit is spent; quality is decided only in stage 5.

```
[1 Brief] → [2 Reference Pack] → [3 Shot List] → [4 Generate] → [5 QA Gate] ─┐
                                                                             │
   ┌─────────────────────────────────────────────────────────────────────-─┘
   │  pass ↓        soft-fail → reinforce refs, re-gen (cap 3)
   │                hard-fail → discard
   ↓
[6 Assemble] → [7 Upload & Publish (HUMAN GATE)] → [8 Measure] ──loop──> [1]/[2]
```

---

## 1 — Campaign brief (intake)

A **campaign** = one product (or SKU family) + one objective + one channel. Define and freeze:

- Product / garment; the **do-not-show** list.
- Objective: awareness / conversion / UGC proof.
- Output format: `9:16` vertical, `1:1` feed, `16:9` site.
- Brand constraints (generic branding only).
- Target shot count and the **shot budget** — e.g. "5 shots, 3 re-roll cap each."

Output: a one-page spec everything downstream inherits from. **The budget is set here.**

## 2 — Reference pack (consistency is won or lost here)

- **Character:** Element (`show_reference_elements`) for Seedance, or Soul (5–20 images) → Soul V2 stills. Keep expression/lighting consistent across refs — variety makes the model average to a generic "midpoint" face.
- **Garment:** flat product shot.
- **Style/mood:** palette + grade reference.
- **Face-filter workaround:** apply a 6×6 grid overlay (full opacity) on any AI-generated face before upload — the filter runs CV on the reference *before* the prompt is read, so rewording does nothing; the fix is on the image.
- Limits to design against: ≤9 images, ≤3 video clips, ≤3 audio clips per generation; ≤15s per shot.

## 3 — Shot list

The coverage sequence, each shot mapped to a beat and a job, written in the two-line grammar (see `prompts.md`):

`wide → medium → detail close-up → tracking → product hero`

Reusable template; fill the variables per campaign. Reusability is the systematic claim.

## 4 — Generate (hybrid)

1. Dial the hero shot in Cinema Studio (UI) until the look is locked.
2. Codify the locked prompt + refs.
3. **Preflight cost:** `generate_video` with `get_cost: true`.
4. Batch the shot list through `generate_video` (model `seedance_2_0`), `count` up to 4 per call.
5. Poll each job with `job_display`; capture `completed / failed / nsfw`.

## 5 — QA gate (the only place quality is decided)

Score every clip on five dimensions (pass/fail, or 1–5):

| # | Dimension | Question |
|---|---|---|
| 1 | **Identity hold** | Does the face stay the same within the clip and across shots? *(#1 failure mode — Seedance trades face for motion under load.)* |
| 2 | **Garment fidelity** | Do logo/crest/stitching stay stable and legible, or warp and invent text? |
| 3 | **Motion plausibility** | Real body + fabric movement, or melting/morphing limbs? |
| 4 | **Prompt adherence** | Did the camera move and framing match the brief? *(Also: did it route to the intended model? The `model` field is only a hint.)* |
| 5 | **Channel fitness** | Right aspect, resolution, pacing for the destination? |

**Automate what you can:** the MCP returns status, so `failed`/`nsfw` are caught before a human looks. Humans only score the passes.

### Signals

**Good:** face locked across cuts; crest/text crisp and unchanging; fabric drapes with real weight; hands/joints correct; lighting consistent shot to shot; camera move reads intentional; no frame-to-frame flicker.

**Bad:** face morphs (ID drift) or settles into a generic average; logo text smears or hallucinates letters; fingers multiply / limbs bend wrong; jelly/warping on motion; temporal flicker on textures; camera instruction ignored; face-filter rejection at upload.

## 6 — Assemble

Cut accepted takes into the sequence; timing; audio. (Seedance accepts an audio reference via `medias` — no `generate_audio` param.)

## 7 — Upload & publish (HUMAN GATE)

Export per channel spec (aspect + resolution; `upscale_video` / `reframe` as needed). **Never auto-publish to a live channel.** Building this gate in deliberately is good ownership, not a limitation.

## 8 — Measure → loop

Capture yield, cost-per-usable, turnaround, then downstream performance (watch-through, CTR; `virality_predictor` for a pre-publish signal). Winning prompts and reference packs get promoted into the template library. This is the loop that compounds — cheaper and better each cycle.

---

## Decision tree (budget-aware)

- **Hard fail** (artifacts, brand violation, wrong product) → discard immediately; do **not** re-roll the same setup.
- **Soft fail** (close, but ID drift or weak motion) → re-generate with **reinforced reference weighting**, not a reworded prompt. If a shot keeps soft-failing, fix it **upstream** (the reference pack), not the prompt.
- **Pass** → select the best passing take; move on.
- **Budget cap** → stop after **3 re-rolls per shot**. The stop-loss is what makes this an economic system, not a creative hobby.
