# prompts.md — the reusable prompt template

Every shot is **two lines**, exactly as the reference editorial decks do it.

1. **Creative-intent line** — the beat in plain English (for the reviewer and the instruction-following layer).
2. **Tag line** — the control vector, in this order:

```
shot type → subject + garment detail → micro-motion → camera move → lighting/tone → depth of field → style → resolution
```

Front-loading **shot type** and **subject** is what keeps the model from drifting. Fill the `{variables}`; everything else is reusable across SKUs.

## Driving a reusable character

- **Element (Seedance):** embed the placeholder `<<<element_id>>>` inside the tag line where the subject goes, e.g. `...athletic kit on <<<a1b2c3...>>> standing confident...`. The backend auto-injects the reference image.
- **Soul → Seedance:** generate the identity-locked still with Soul V2, then pass that still as a Seedance `medias` entry with role `start_image` instead of an inline placeholder.

---

## 5-shot coverage template

> Variables: `{product}` `{subject}` `{setting}` `{move}` `{grade}`

**Shot 1 — Wide / establish.**
> *The scene establishes: {subject} in {setting}, full body, the world around them.*
>
> `wide establishing shot, {subject} wearing {product}, slight weight shift, slow dolly in, {setting} ambient light, deep depth of field, premium editorial, 8k`

**Shot 2 — Medium / connect.**
> *We close to a medium; the product reads clearly on the body.*
>
> `medium shot waist-up, {subject} in {product} with crisp detail, subtle breathing, slow pan, soft key light, medium depth of field, {grade}, 8k`

**Shot 3 — Detail close-up.**
> *Hero the product detail — crest, stitching, texture.*
>
> `extreme close-up, {product} crest and stitching, fabric breathing, slow push-in, raking light to show texture, shallow depth of field, {grade}, 8k`

**Shot 4 — Tracking.**
> *Motion shot — the product in dynamic use.*
>
> `tracking shot, {subject} in {product} mid-{move}, natural body and fabric motion, lateral tracking follow, directional light, shallow depth of field, {grade}, 8k`

**Shot 5 — Product hero.**
> *The closing money shot — confident, still, premium.*
>
> `hero product push-in, {subject} in {product} standing confident, subtle breathing and weight shift, slow push-in settling on detail, {setting} bokeh behind, shallow depth of field, cinematic {grade}, 8k`

---

## Hero shot (filled — the proof-of-taste deliverable)

> Variables: `{subject}` = original idol-styled character (NOT a real celebrity) · `{product}` = generic England-style football kit · `{setting}` = World Cup–style stadium · 9:16, ~8–10s.

> *Hero / product push-in. The model stands on the pitch as the stadium lights bloom behind; a slow push-in settles on the kit crest as fabric breathes.*
>
> `slow push in, athletic football kit with subtle crest, model standing confident on stadium pitch, subtle breathing and slight weight shift, crest centered and stable, floodlit stadium bokeh behind, shallow depth of field, cinematic warm-cool grade, premium sport editorial, 8k`

**Params:** `model: seedance_2_0` · `aspect_ratio: 9:16` · `duration: 8–10` · `resolution: 720p` · `mode: std` · refs: character (Element or Soul start-frame) + garment ref + stadium style ref.

**QA pre-fill for this clip:** watch identity hold under the push-in; watch the crest for warp/invented text; confirm floodlight bokeh doesn't flicker; confirm the push-in reads as intentional (not a zoom artifact). Cap: 3 re-rolls; reinforce refs on soft fail.
