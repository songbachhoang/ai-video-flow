# PLAN — ai-video-flow

A repeatable AI **video-production system** (not a single clip) plus one hero video as proof of taste — the deliverable for the Crossian PM/PO audition. Platform is [Higgsfield](https://higgsfield.ai) (Seedance 2.0 model family) driven by Claude Code over the hosted MCP. Principle: **UI for taste, MCP for scale.**

Verified-at: 71ef2eae3801262e02790d1ded67860506c0e720 · 2026-06-10

<!-- sync:status:start -->
## Status

### Done
- **System docs** — `CLAUDE.md` (operating contract: default params, identity architecture, toolmap, golden rules incl. never-auto-publish), `pipeline.md` (8-stage playbook + QA rubric + budget-aware decision tree), `prompts.md` (shot-list grammar / reusable template), `README.md` (overview).
- **`/make-campaign` slash command** — `.claude/commands/make-campaign.md`: one command runs brief → reference pack → shot list → preflight cost → batch generate → QA gate → log → roll-up, stopping at the human publish gate.
- **Hero video generated** — Seedance 2.0 Fast, 720p, 9:16.
- **Two cuts assembled with ffmpeg** — a 2-angle hero cut (wide → close, ~10s) and a scripted 4-shot voiced "story ad" (ritual → departure → city → hero, ~21.5s). Exports live in `output/` (gitignored); the story ad is published into the site as `docs/media/story.mp4`.
- **Logs populated with real data** — `qa-log.csv` (7 per-clip rows: 5-dimension scores, job IDs, verdicts, actual credits) and `metrics.csv` (3 campaign roll-ups: yield, real cost-per-usable, turnaround). Numbers are measured, not estimated.
- **Repo public + GitHub Pages live** — `songbachhoang/ai-video-flow`, served from `main` `/docs` at https://songbachhoang.github.io/ai-video-flow/ (build status: built, HTTPS enforced).
- **Pitch page** — `/docs` rewritten as a first-person "How I'd run your video engine" editorial: a 90-day plan, a before/after workflow diagram, and the embedded story-ad proof.

### In progress / open tweaks
- Match Bach's writing voice across the pitch page copy.

### Pending (optional, gated on budget/decisions)
- Full 5-shot campaign run end-to-end and a fuller `metrics.csv`.
- Premium voiceover (e.g. ElevenLabs) to replace the current system TTS bed.
- Custom domain for the Pages site (currently the default `*.github.io` URL).

## Known constraints
- **Budget:** Higgsfield **Starter** plan, ~tapped out (a few credits left) — Seedance 2.0 **Fast**, 720p. A full re-run needs a top-up or plan change. (Note: `CLAUDE.md` references a Plus $49/mo tier as a general pricing example; the actual subscription used here is Starter.)
- **Quality ceiling:** Seedance Fast at 720p loses micro-detail (fine garment patterns / small marks soften); prominent shapes and identity hold well.
- **Guardrails (non-negotiable):** original characters only (no real-celebrity likeness), generic branding by default, and **never auto-publish** — generation/assembly is automated end-to-end but the post to any live channel is always a human decision.
<!-- sync:status:end -->

## How to run

Prereq: Higgsfield MCP connected (OAuth, no API keys) and the workspace has credits.

```
/make-campaign <SKU-name> [shot-count]
```

Runs the full pipeline for one SKU (preflight → batch generate → QA gate → log to `qa-log.csv` → roll up to `metrics.csv`), enforces the 3-re-rolls-per-shot cap, and stops at the human publish gate. See `CLAUDE.md`, `pipeline.md`, and `prompts.md` for the rules, rubric, and templates.

Preview the pitch site locally by opening `docs/index.html` in a browser (it is a static page; `docs/media/` holds the stills and the story-ad mp4). The published version is the GitHub Pages URL above.
