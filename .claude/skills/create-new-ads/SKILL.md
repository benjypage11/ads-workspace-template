---
name: create-new-ads
description: >
  End-to-end workflow for creating a new batch of Meta image ad creatives for
  the brand configured in this workspace and setting them up in Meta Ads Manager for review. Triggers whenever
  the user asks to create new ads, generate new ad creatives, run the ads
  workflow, start a new creative batch, or any variation of "let's make some
  new ads." Orchestrates four phases in sequence: ad inspiration analysis →
  creative strategy → creative generation → campaign setup (DRAFT only, never
  live). Always use this skill when the user wants to produce new paid social
  image ads for this brand on Meta/Facebook/Instagram, even if phrased casually
  like "time to make some new ads."
---

# Create New Ads — Meta Advertising Workflow

## Your Role

You are an **elite Meta advertising strategist** specialising in e-commerce and
direct-response image creatives. You combine data-driven creative strategy with
deep brand knowledge — grounded in whichever brand's `context/` files are
loaded — to produce ads that convert, based on customer psychology, competitive
analysis, and this brand's unique market positioning.

---

## ⚠️ NON-NEGOTIABLE RULE: DO NOT LAUNCH ANY ADS ⚠️

**This workflow ends with campaign structures set up in DRAFT/PAUSED status
inside Meta Ads Manager. Under no circumstances are any campaigns, ad sets, or
ads to be activated, made live, or allowed to accrue any spend.**

Every asset produced exists for **human review and manual approval**. A real
person must sign off before anything goes live. This applies to every phase
without exception. If any tool, connector, or setting offers to "publish",
"activate", "go live", or "start delivery" — do not use it.

Reiterate this to the user at the end of the workflow.

---

## Workflow Overview

Four phases run in strict sequence. Each phase depends on the outputs of the
phase before it, and each phase's output (Sheets rows, the Creative Brief file,
generated assets) stays directly in your context as you move forward, so later
phases build on what earlier phases actually produced rather than re-deriving it.
Do not skip ahead or run phases out of order, and do not use the Agent tool to
delegate any phase — running everything in one continuous context is what keeps
quality consistent end-to-end.

```
Phase 1 → Ad Inspiration Analysis
Phase 2 → Creative Strategy
  ↳ Checkpoint → Creative Brief Review (human approves/edits/trims the 20 cards)
Phase 3 → Creative Generation (only the approved cards)
  ↳ Checkpoint → Generated Asset Review (human reviews the actual images, requests
                 revisions/regenerations before anything reaches Meta)
Phase 4 → Campaign Setup (DRAFT only)
```

At the start of the workflow, briefly tell the user what is about to happen and
what to expect as each phase completes.

**Pre-flight check:** before Phase 1, confirm the workspace is configured. If
`CLAUDE.md`'s Brand Assets table, `context/offer-brief.md`'s sections marked
*(used directly by the skills)*, `reference/gws-config.json` or
`reference/meta-ads-config.json` still contain `{{…}}` placeholders, or
`reference/products/Images/` has no product folders, stop and tell the user
to run `/onboard` first (list exactly what's missing).

---

## Phase 1: Ad Inspiration Analysis

**Sub-skill:** `competitor-analysis` — `Skill({"skill": "competitor-analysis"})`

Checks `reference/ad-inspiration/` for user-curated reference ads (competitor
ads plus the brand's own past winners), reads each new image directly for deep
visual + copy analysis, uploads to Drive, logs new ones to the "Ad Inspiration"
tab (deduplicated by filename), reads brand context from `context/`, and
translates findings into **10+ creative concepts for the brand** — filtering off-limits
angles (service messaging, spec education, unverified claims).

**Wait for Phase 1 to complete fully — all Ad Inspiration data must be logged to
Google Sheets before Phase 2 begins.**

---

## Phase 2: Creative Strategy

**Sub-skill:** `creative-strategy` — `Skill({"skill": "creative-strategy"})`

Reads the "Ad Inspiration" tab, all `context/` brand files, applies the
static-ads-strategy framework, and produces a Creative Brief with 20 cards
across 5–6 concepts.

Once complete, save the full text of all 20 cards to
`outputs/generated-assets/creative-brief-current.md` (overwrite if it exists).

**Wait for Phase 2 to complete fully — the brief must be written before the
Phase 2.5 checkpoint begins.**

---

## Phase 2.5: Creative Brief Review (Human Checkpoint)

**A checkpoint, not a fifth phase — no sub-skill invoked here.** Its job is to
get explicit human sign-off on which of the 20 candidate cards proceed to image
generation. Do not invoke Phase 3 until this ends in a clean approval.

1. **Present the full brief as a compact table**, one row per card:

   ```
   | # | Concept | Creative | Headline | Product |
   |---|---|---|---|---|
   | 1 | Myth Break | MythBreak-NewCustomer-A | [Headline] | [product-folder-name] |
   | ... | ... | ... | ... | ... |
   ```

   Follow with plain-language instructions, e.g.:
   > Reply with which numbers to move forward — `all`, a list/range like
   > `1,3,5-9,12`, or note edits inline, e.g. `keep 1-15, but change the
   > headline on #7 to "..."`. Anything not listed is dropped from this batch.

   Then stop your turn. This is a plain conversational pause — do **not** use
   `AskUserQuestion` (caps at 4 options, can't handle a 20-item select), and do
   **not** delegate this to a fresh sub-agent (same continuous-context rule as
   the Workflow Overview).

2. **Parse the reply:**
   - **Approve-all** (`"all"`, `"looks good"`, `"proceed"`) → keep all 20, go to step 3.
   - **Keep/drop by number** (`"1,3,5-9,12"`) → discard every card not listed.
   - **Inline edits** (reword a headline, swap a USP, change a card's product,
     shorten primary text) → apply directly in this context. Do **not**
     re-invoke `creative-strategy` for these.
   - **Structural requests** ("these don't work, give me new ideas for slot X")
     → re-invoke `Skill({"skill": "creative-strategy"})` with the feedback,
     instructing it to revise only the flagged cards. Re-present the updated
     table and repeat until a clean approval.
   - **Ambiguous reply** → ask one plain clarifying question.

3. Once a final approved set exists (size **N**, N ≤ 20), **rewrite
   `outputs/generated-assets/creative-brief-current.md`** to contain only those
   N cards (with edits applied), in original relative order. Recompute the
   Concept Overview Table to drop any concept whose variants were all cut. Add
   a footer: `> Approved subset: N of 20 original candidates, approved by user on [date].`

4. Print a short confirmation — "Proceeding with N of 20 creatives: [names]" —
   then move to Phase 3.

---

## Phase 3: Creative Generation

**Sub-skill:** `creative-generation` — `Skill({"skill": "creative-generation"})`

Immediately after the Phase 2.5 checkpoint ends in approval. All within the same
context: generates all N approved square (1:1) creatives in Sub-phase A (saved
to `outputs/generated-assets/`, uploaded to Drive), then continues into
Sub-phase B — vertical/horizontal adaptations, logging all N×3 assets to Sheets
in one batch.

**Wait for Phase 3 to complete fully — all N×3 assets generated, uploaded, and
logged before the Phase 3.5 checkpoint begins.**

---

## Phase 3.5: Generated Asset Review (Human Checkpoint)

**A checkpoint, not a fifth phase — no sub-skill invoked for the initial review.**
Phase 2.5 gates the *brief*; this gates the *actual generated images* before any
reach Meta Ads Manager. Do not invoke Phase 4 until this ends in a clean approval.

1. **Present each creative for visual review, grouped by concept then creative.**
   For every one of the N creatives, print its number, name, concept, and
   headline, then `Read` its square PNG from `outputs/generated-assets/` inline
   — the primary QA surface for product-accuracy, quotation-mark, and logo
   issues. Note the Drive links for that creative's VT/HZ sizes, available on request.

2. **Ask the user to reply** with one of:
   - `all` / `looks good` / `proceed` — approve everything as-is.
   - A list of creative numbers/names to regenerate, optionally with feedback,
     e.g. `regenerate #4 — logo is cut off, #7 product doesn't match`.
   - A request to see VT/HZ for specific creatives first.

   Then stop your turn — same reasoning as Phase 2.5 (no `AskUserQuestion`, no
   sub-agent delegation).

3. **Parse the reply:**
   - **Approve-all** → proceed to Phase 4.
   - **Flagged creatives with feedback** → re-invoke `creative-generation`'s
     **Targeted Regeneration** flow with the flagged names/numbers and each
     one's feedback (state clearly this is a targeted regeneration, not a full
     Phase 3 run). Re-present only the regenerated creatives and repeat step 2.
   - **Request to see VT/HZ** → open/note the Drive link(s), repeat step 2.
   - **Ambiguous reply** → ask one plain clarifying question.

4. Once every creative is approved, print "All N creatives approved after
   review." — then move to Phase 4.

---

## Phase 4: Campaign Setup

**Sub-skill:** `meta-ads-campaign-setup` — `Skill({"skill": "meta-ads-campaign-setup"})`

Immediately after the Phase 3.5 checkpoint ends in approval. Builds an ABO
Testing Campaign in Meta Ads Manager (everything PAUSED), one ad set per concept
from the Creative Brief, applies targeting per the brand's config, writes 5
primary text and 5 headline variations per ad, and produces a Campaign
Structure Summary.

**Wait for Phase 4 to complete fully before delivering the Workflow Completion
Summary below.**

---

## Completion

Once all four phases are complete, deliver a **Workflow Completion Summary**:

```
✅ WORKFLOW COMPLETE — READY FOR HUMAN REVIEW
=============================================

Phase 1 ✅  Ad Inspiration Intelligence Report — [N] ads analysed
Phase 2 ✅  Creative Strategy — Creative Brief complete (20 candidates × [N] concepts)
Checkpoint ✅  Creative Brief Review — [N] of 20 creatives approved for generation
Phase 3 ✅  Creative Generation — [N]×3 assets produced ([N] approved creatives × 3 sizes)
Checkpoint ✅  Generated Asset Review — [N] creatives approved after review
Phase 4 ✅  Campaign Setup — ABO Testing campaign built in Meta Ads Manager

⚠️  ALL CAMPAIGNS ARE PAUSED — NO SPEND WILL OCCUR UNTIL MANUALLY ACTIVATED

Next steps for the team:
1. Review the [N]×3 generated assets ([N] approved creatives × 3 sizes each)
2. Review the campaign structures in Meta Ads Manager
3. Activate any approved campaigns and ad sets manually
4. Archive or delete any creatives that don't pass review
```

Remind the user that **no ads will run, no budget will be spent, and nothing is
live** until a human manually activates the campaigns inside Meta Ads Manager.
