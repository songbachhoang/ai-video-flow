# ai-video-flow

A **production system** for turning any SKU into on-brand video on demand — not a single clip, a repeatable flow.

> You don't want someone who can make one good video. You want a flow that makes the 400th product video as cheap and on-brand as the first. Here is the system, here are the unit economics, here is how it scales across a catalog.

That thesis is the point of this repo. It is the marketing arm of an inventory→demand loop: feed it a product (including deadstock you need to move), get back channel-ready video, with cost-per-usable and yield measured every cycle.

---

## How it works

```
[1 Brief] → [2 Reference Pack] → [3 Shot List] → [4 Generate] → [5 QA Gate] ─┐
                                                                             │
   ┌─────────────────────────────────────────────────────────────────────-─┘
   │  pass ↓        soft-fail → reinforce refs, re-gen (cap 3)
   │                hard-fail → discard
   ↓
[6 Assemble] → [7 Upload & Publish (HUMAN GATE)] → [8 Measure] ──loop──> [1]/[2]
```

**Principle: UI for taste, MCP for scale.** Dial each look in Higgsfield's Cinema Studio until it's locked, then codify the locked prompt + reference pack into MCP calls and run production in batch. The first unit is artisanal; the Nth is automated.

## Stack

- **Platform:** [Higgsfield](https://higgsfield.ai) (15+ models, one credit pool) via the official hosted **MCP** server (`https://mcp.higgsfield.ai/mcp`, OAuth, no API keys).
- **Primary video model:** Seedance 2.0 (ByteDance) — reference-driven, identity-consistent, multi-SKU.
- **Identity:** Soul V2 for identity-locked stills → fed into Seedance as start frames; or reusable **Elements** referenced directly inside Seedance prompts.
- **Driver:** Claude Code, reading `CLAUDE.md` every session.

## Run a campaign

Once Higgsfield MCP is connected and the workspace has credits:

```
/make-campaign <SKU-name>
```

That reads `prompts.md`, preflights credit cost, batch-generates the shot list through Seedance, polls each job, runs the QA gate, logs every clip to `qa-log.csv`, enforces the 3-re-roll budget cap, and reports yield + cost. It never publishes to a live channel — that stays a human decision.

## What's in here

| File | What it is |
|---|---|
| `CLAUDE.md` | Operating contract — rules, default model/params, toolmap. Read by Claude Code every session. |
| `pipeline.md` | The 8-stage playbook + QA rubric + decision tree + budget cap. |
| `prompts.md` | Shot list in the two-line prompt grammar, with `{product}/{setting}/{move}` variables. The reusable template. |
| `qa-log.csv` | Per-clip scores, job IDs, pass/fail, re-roll count, credits. |
| `metrics.csv` | Per-campaign yield, cost-per-usable, turnaround, shots/product. |
| `.claude/commands/make-campaign.md` | The `/make-campaign` slash command. |

## Guardrails (non-negotiable)

- **Never auto-publish** to a live channel. Generation is automated end-to-end; the post is manual.
- **No real-celebrity likeness.** Original characters only.
- **Generic branding.** No reproduction of real trademarked kit/sponsor marks.
- **Budget cap:** 3 re-rolls per shot, then stop. A runaway shot quietly eats campaign margin.
