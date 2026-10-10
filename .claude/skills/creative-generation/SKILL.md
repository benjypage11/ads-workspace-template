---
name: creative-generation
description: >
  Accepts the Creative Brief from creative-strategy — after the Phase 2.5 human
  review checkpoint has approved a subset of N cards (N ≤ 20) — and executes
  visual production: crafts Higgsfield image prompts from each approved brief
  card, generates all N square (1:1) assets in Sub-phase A, then continues
  directly into Sub-phase B in the same context (vertical + horizontal
  adaptations + batch Sheets logging). Invoke for Phase 3 of the Create New Ads
  workflow. Always run after creative-strategy has produced a complete Creative
  Brief and it has been reviewed at the Phase 2.5 checkpoint.
---

# Creative Generation — Meta Ad Creatives

## Terminology — Read This First

- **Concept** — a strategic angle, message, or style (e.g., Myth Break,
  Testimonials, Before/After, Trust Seal). An idea, not an image.
- **Creative** — an individual ad image. Multiple creatives are produced per
  concept, each a variation within that concept (different product/variant, visual
  treatment, or copy angle).

Each concept should produce **3–4 creatives**. Each creative is produced in
**3 size variants** (square, vertical, horizontal).

---

## Before You Start

1. **Confirm the Creative Brief is in context** — the approved cards from
   creative-strategy's Phase 2.5 checkpoint must be available. Count the cards
   actually present in `outputs/generated-assets/creative-brief-current.md` to
   determine **N** (N ≤ 20 — the checkpoint may have trimmed the original 20).
   Do not assume N is 20. If cards are missing entirely, return to creative-strategy.

2. **List eligible products:**
   ```bash
   ls "reference/products/Images/"
   ```
   This is the authoritative, exhaustive list of products eligible for this
   batch. Cross-check the `Product to feature` value in each brief card against
   this listing — flag any brief card referencing a folder not present. For
   each distinct product referenced, `ls "reference/products/Images/<exact folder name>/"`
   to confirm its images. Always use the exact folder name string `ls` returns
   — never hardcode one.

3. **Read the brand config and product notes:** `CLAUDE.md`'s **Brand Assets**
   section (colors, headline/body fonts and the headline font's description,
   primary logo file) and the *Product photography quirks* section of
   `reference/products/README.md`. Every prompt below substitutes these values
   wherever this skill says [primary color], [headline font], [body font] or
   [logo file].

4. **Read the prompt engineering reference:**
   📄 `.claude/skills/image-prompting/prompt-engineering.md`

   Every generation prompt must use **prestigious context anchors** instead of
   banned keywords and must never be written as bare comma-separated tags. The
   Branded Paid Social Ad template (used for all creatives) is a
   structured markdown spec with `## Design Layout:` and `## Visual Style:`
   sections, not flowing narrative prose — follow that template's exact
   structure (see below) rather than freestyling a paragraph.

5. **Confirm the Creative Brief checkpoint file exists:**
   `outputs/generated-assets/creative-brief-current.md` should already exist —
   `create-new-ads` saves the full brief there after Phase 2, then rewrites it
   to the approved subset after Phase 2.5. Check for it; only if missing (e.g.
   running this skill standalone with no checkpoint run) should you write the
   full text of all cards there yourself first. This file is your reference for
   Sub-phase B and a checkpoint to resume from if generation is interrupted.

---

## Assets to Use

### Product Images

Product images live under `reference/products/Images/<folder>/`, one folder per
product/variant, matching the Creative Brief's `Product to feature` field
exactly. There are no SKUs or per-product spec files — filenames describe
content directly (see `reference/products/README.md`), e.g.:

| Pattern | Type | Use for |
|---|---|---|
| `*-hero.png`, `*-packshot.png` (or packaging-named, e.g. `*-pouch.png`, `*-bottle.png`) | Clean packaging/hero shot | Product-hero, trust-callout, direct-response creatives |
| `*-lifestyle.png`, `*-ingredient-background.png`, `*-in-use.png` | Lifestyle/context shot | Lifestyle, transformation, social-proof creatives |
| `*-graphic-*` | Supporting marketing graphic | Trust/education-adjacent concepts, used sparingly |

Source specs/claims (dosage, certification, ingredients) from
`context/offer-brief.md` / `context/business-info.md` — never invent a claim
that isn't there.

**Lifestyle scenes must ground every loose product piece — never let them float.**
When a brief card's `Visual direction` names a realistic lifestyle scene (kitchen
counter, gym, desk, hand-holding, etc.), any loose product pieces referenced in
the prompt (capsules, gummies, beans, accessories) must be grounded — resting on
a surface, in a hand, in a bowl — never floating or tumbling. Floating/tumbling
props remain an available creative choice for stylized, product-focus-only scenes
with no realistic setting — this is still a per-card decision driven by the
brief's scene mode, not a blanket rule either way. See
`image-prompting/prompt-engineering.md`'s Floating vs. Grounded Props rule.

**Apply the Product photography quirks.** If `reference/products/README.md`
records that reference photos have something baked in that a given card doesn't
want (e.g. floating product pieces beside the pack), the prompt must explicitly
override it (e.g. "Remove the floating pieces next to the pack") whenever the card
wants the grounded look. When a stylized card wants to keep that look, omit the
override and let the reference photo stand.

**Think carefully about which references this specific creative actually
needs — don't upload a fixed set by habit.** A clean packaging/hero shot
alone may be enough for the product photography to render accurately depending on what the creative brief calls for. For other creative brief cards however, it may be necessary to
add the lifestyle/ingredient shot as a reference. Also consider
`reference/design-assets/` — matched background textures, USP/benefit
icon sets, and press/trust logos live there and should be uploaded as
`medias` references whenever the brief card's concept, badge, or background
treatment calls for one (this is separate from the mandatory brand logo,
covered in its own non-negotiable section below). Pass whatever is chosen as
`medias` references to Higgsfield — this gives the model an image reference
and provides more accurate product fidelity and branding.

### Brand Logo — Upload Whenever the Brief Card Includes One

> ⚠️ **The logo file MUST be uploaded via `media_upload` and passed as a
> `medias` reference on EVERY generation call for a card whose brief specifies
> a logo — square, vertical, and horizontal. Mentioning the logo in prompt
> text alone does NOT work. Without the file upload, Higgsfield cannot render
> the actual logo. Any such creative missing the logo upload must be
> regenerated immediately.**

Check the brief card's `Logo:` field first:

- **If "Yes"** (typically ~3 in 4 cards in a batch — see `creative-strategy`'s
  Logo Frequency rule): the logo is always the **primary logo file** named in
  `CLAUDE.md`'s Brand Assets (the icon+wordmark lockup) — never an icon-only
  mark, which can hallucinate into a distorted hybrid when uploaded as a
  reference. Position it centred at the top of the image with clear open space
  around it — not touching the frame edge, headline, or badge. This position is
  fixed, not varied per creative.
- **If "No":** skip the logo entirely for that card — no upload, no prompt
  mention.

Secondary marks (checkmark/seal versions of the logo) are **not** the main
logo — reserve them for illustrating a specific claim or USP callout (e.g. a
checkmark icon next to a certification), never as the primary logo upload.

**Before calling `generate_image` for any size on a card that includes the
logo, confirm the primary logo file has been uploaded via `media_upload` and
its ID is in the `medias` array.**

### Brand Visual Identity — Apply to Every Prompt

See `CLAUDE.md`'s **Brand Assets** section for the canonical colors/fonts table.
Every prompt's headline must read exactly `"...in [headline font] font
([headline font description])..."` — name AND describe it together, every time,
using the verbatim description from Brand Assets; custom/licensed fonts are
fonts the model has never seen, and the name alone produces inconsistent output
(e.g. an unwanted heavy/bold weight). Every other text overlay (subline, badge,
additional copy) names the body font (e.g. "in white [body font] text"), adding
its description too if Brand Assets provides one. Every prompt must reference
the **primary color hex** as the accent colour (logo strip, headline colour, CTA
bar, or design element). These are non-negotiable — without them the output uses
arbitrary fonts/colours/weights with no relationship to the brand.

**Product-matched accent — USP Icon Row, never a lone icon:** when
`reference/design-assets/` holds an icon set in several color variants, use the
variant that matches the featured product's color/flavor rather than defaulting
to the brand's primary color for every creative.

**These icons are only ever rendered as a themed group of 3–4 in a single
horizontal row, centered horizontally, each icon paired with a short grounded
benefit label (sourced from `context/offer-brief.md`/`context/business-info.md`
— never invented) directly beneath or beside it, all icons in the same color
variant, no background box behind any of them — never as a single, lone,
unlabeled icon accent dropped into a corner.** See
`image-prompting/prompt-engineering.md`'s USP Icon Row rule for the exact
Design Layout bullet pattern and the proven prompt language to use (Example 4
there is the confirmed-correct reference). If a brief card's `Visual direction` only calls for "a small icon
accent" with no group and no labels, expand it into a full 3–4 icon+label row
before prompting — never generate a lone icon.

---

## Generation Tool

Use the **Higgsfield connector** (`generate_image` tool) to generate all creatives.

- **Model:** `gpt_image_2` (GPT Image 2 by OpenAI) — do NOT use
  `marketing_studio_image` or any other model unless explicitly instructed.
  Verify availability with `models_explore` if needed.
- **Quality: always `"high"`** — never the default (low). Low quality produces
  blurry, unusable output. Pass `quality: "high"` explicitly on every call.
- Before EVERY generation call (square, vertical, horizontal), upload via `media_upload`:
  1. The product packaging/hero image for that creative
  2. A lifestyle/ingredient-context image from the same product folder (if available)
  3. **The primary logo file** (from `CLAUDE.md`'s Brand Assets) — mandatory
     on every call for a card whose brief specifies a logo (~3 in 4 cards); never
     substitute an icon-only mark. Skip entirely for a card whose brief
     specifies no logo.
  4. Any relevant design asset(s) from `reference/design-assets/` this
     creative's brief card calls for (matched background, USP icon row,
     press/trust logo) — only when the concept/badge/background
     treatment needs one, not by default.
- Pass all as `medias` with role `"image"` in the generate call.
- **Never call `generate_image` without the primary logo file in the
  `medias` array for a card that includes the logo.**

---

## Size Variations — 3 Required per Creative

| Size | Dimensions | Aspect ratio | Placement use |
|---|---|---|---|
| Square | 1080 × 1080 px | 1:1 | Feed (Facebook & Instagram) |
| Vertical | 1080 × 1920 px | 9:16 | Stories & Reels |
| Horizontal | 1920 × 1080 px | 16:9 | Facebook Feed / Right Column / Display |

### Vertical (9:16) Composition — Adapt Naturally, Don't Cram Upward

> Stories/Reels overlay their own UI (caption, audio track, engagement bar) in
> a strip at the very bottom of the frame — this is the **"Danger Zone."**
> **Leave the bottom ~25% of the frame clear (roughly the bottom ~450px of a
> 1920px-tall image)** — don't compress the whole layout upward to compensate.
> Use the same text/logo placement and hierarchy that worked in the square as
> your reference point; only the bottom Danger Zone needs to stay clear of
> key design elements (logo, headline, product).

---

## Generation Batching — Run in Small Concurrent Batches

Both sub-phases produce a large number of assets (N squares in Sub-phase A;
N×2 adaptations in Sub-phase B, N ≤ 20).

**Run in batches of 3–4 concurrent generations.** Within each batch: prepare
every creative's prompt and media uploads first, fire that batch's
`generate_image` calls together, wait for all to finish, then
download/save/upload-to-Drive and print the summary line for each before moving
to the next batch. This meaningfully cuts wall-clock time and token overhead
versus one-at-a-time, while avoiding the rate-limit/context-overload cutoff
seen with full parallelism.

If interrupted, resume from `phase3a-state.json` (Sub-phase A) or the
per-creative Drive uploads and printed summary lines already produced
(Sub-phase B) — no separate batch-tracking file is needed.

---

## Sub-phase A — Generate All N Square (1:1) Assets

**Run this first. Do not generate any vertical or horizontal assets yet.**

Split the N creatives into batches of 3–4, work through them in brief order.

**For every creative in the current batch, first (Steps 1–2):**

**Step 1 — Craft the generation prompt**

**Use the Branded Paid Social Ad (Lifestyle/Product) template**, which lives in
`.claude/skills/image-prompting/prompt-engineering.md`. Follow the template's
**`## Design Layout:` / `## Visual Style:` markdown structure** closely rather
than freestyling it or reverting to a narrative-prose paragraph — Design Layout
covers background, logo (only if this card's brief includes one), social proof
badge, main product, and any supporting props; Visual Style covers scene mood,
lighting, camera spec, and the prestigious context anchor. Tweak the content per
the specific concept/creative being generated, but keep the two-section
structure.

**Point the model at the reference image for the product — don't re-describe
it.** Name only the generic product-type noun (supplement pouch, skincare bottle,
coffee bag, sneaker, etc.) so the sentence is grammatical, then instruct the
model to use the attached images for everything else, e.g. `"The main visual:
the [product type] shown in the images attached"`. The brief card's product folder name is for
locating files, not for prompt text — the uploaded images are the single
source of truth for appearance; let the model copy them.

**First, check whether this brief card carries an on-image badge.** Per
`creative-strategy`'s Brief Card, roughly half the batch's cards specify one and half
don't (see `static-ads-strategy`'s Principle 3) — cards without one still carry the
proof point via primary/caption text only, and get no social-proof element in the
image prompt at all.

When a badge is called for, **choose one visual treatment from the menu** in
`image-prompting/prompt-engineering.md`'s Social Proof Badge — Visual Variety rule,
rotating treatments across the batch rather than defaulting every creative to the same
orange rectangle. If the proof is review/rating-based, include a rendered graphic star
element (★★★★★), never text alone. **Name the social proof element once, at one named
position** — state that position a single time.

Include any additional copy specified in the brief card (badge, price anchor)
— only elements explicitly present in the brief, never an invented claim.
Check the brief card's `Logo:` field — if "Yes," include the logo bullet
(always centred at the top of the image, clear open space, no per-card
position variation); if "No," omit the logo bullet entirely.

**Retain this exact prompt text** — carry it through unchanged into
`phase3a-state.json` as `sq_prompt` so it can be logged to Sheets verbatim. Do
not paraphrase or reconstruct it later. Also note the background/setting
treatment and the prestigious context anchor used, carried into
`phase3a-state.json` as `background_description` and `mood_anchor` so
Sub-phase B can restate them precisely.

**Step 2 — Upload media references (logo upload is mandatory when the card has one)**
1. The product packaging/hero image from `reference/products/Images/<Product to feature>/`
2. A lifestyle/ingredient-context image from the same folder (if one exists)
3. **The primary logo file** (from `CLAUDE.md`'s Brand Assets) — required
   whenever this card's brief `Logo:` field is "Yes." Never substitute an
   icon-only mark. Skip this upload if the field is "No."
4. Any relevant design asset(s) from `reference/design-assets/` this
   creative's brief card calls for (matched background, USP icon row,
   press/trust logo) — only when the concept/badge/background treatment
   needs one, not by default.

**Once every creative in the batch has its prompt and media ready (Step 3):**

**Step 3 — Generate the squares (1:1) concurrently**
Fire `generate_image` for every creative in the batch together — `model:
"gpt_image_2"`, `quality: "high"`, dimensions 1080×1080, all media references
for each. Wait for all jobs to complete — suppress intermediate polling output.

**Then, for each creative in the batch in turn (Steps 4–7):**

**Step 4 — Download and save locally**
`outputs/generated-assets/[CreativeName]-SQ_[DD-MM-YYYY].png`.

**Step 5 — Upload to Google Drive**
Read the Drive folder ID from `reference/gws-config.json`, then:
```bash
gws drive files create \
  --json '{"name": "[CreativeName]-SQ_[DD-MM-YYYY].png", "parents": ["DRIVE_FOLDER_ID"]}' \
  --upload "outputs/generated-assets/[CreativeName]-SQ_[DD-MM-YYYY].png" 2>/dev/null
```
Capture the returned file `id`. Construct the Drive link:
`https://drive.google.com/file/d/FILE_ID/view`

**Step 6 — Print the summary line (suppress all other output)**
```
✅ Creative [N] — [concept name] — SQ complete | Drive ✓
```

**Step 7 — Do NOT log to Sheets. Do NOT generate VT or HZ.**

Then move to the next batch, repeating Steps 1–7, until all N squares exist.

### After all N squares are complete

Write `outputs/generated-assets/phase3a-state.json` — an array of N objects,
one per creative, in brief order:

```json
[
  {
    "n": 1,
    "name": "ConceptName-Treatment-Variant",
    "conceptName": "Concept Group Name",
    "sq_local_path": "outputs/generated-assets/ConceptName-Treatment-Variant-SQ_DD-MM-YYYY.png",
    "sq_drive_link": "https://drive.google.com/file/d/FILE_ID/view",
    "sq_prompt": "The exact, full Higgsfield prompt text used to generate the square in Step 1 — verbatim, not reconstructed",
    "headline": "Exact headline from brief card",
    "subline": "Exact subline from brief card or empty string",
    "social_proof": "Social proof element from brief card",
    "primary_text": "Full primary text from brief card",
    "logo_included": "true|false — from this creative's brief card Logo: field",
    "background_description": "The background/setting treatment used in this creative's square, e.g. \"soft citrus-orange gradient studio backdrop\" — captured in Step 1, reused verbatim in Sub-phase B",
    "mood_anchor": "The prestigious context anchor used in this creative's square, e.g. \"Men's Health wellness product editorial\" — captured in Step 1, reused verbatim in Sub-phase B",
    "vt_prompt": "Filled in during Sub-phase B: the exact bespoke vertical adaptation prompt sent for this creative, verbatim",
    "hz_prompt": "Filled in during Sub-phase B: the exact bespoke horizontal adaptation prompt sent for this creative, verbatim"
  }
]
```

## Sub-phase B — Generate Vertical & Horizontal Adaptations for All N Creatives

**Continue directly into Sub-phase B in the same context.** Use the Creative
Brief and `phase3a-state.json` already in hand from Sub-phase A.

Your job now:
1. Generate the vertical (9:16) and horizontal (16:9) adaptations for all N creatives
2. Upload each asset to Google Drive as it is generated
3. Log all N×3 assets (SQ + VT + HZ for all N) to Google Sheets in one batch at the end

Before writing any adaptation prompts, (re-)read
`.claude/skills/image-prompting/prompt-engineering.md` — specifically the
**Vertical Adaptation Prompt** and **Horizontal Adaptation Prompt** patterns.
Vertical and horizontal prompts follow those patterns exactly.

GENERATION RULES (model, quality, naming, polling — same as Sub-phase A; see
"Generation Standards Checklist" below):
- Model: gpt_image_2, quality: high — always
- Vertical: 1080×1920 px (9:16)
- Horizontal: 1920×1080 px (16:9)

Work through the N creatives in `phase3a-state.json` in the same batches of 3–4
used in Sub-phase A (in `n` order). For each batch, run the vertical pass for
the whole batch first, then the horizontal pass — see "Generation Batching"
above.

**Three media references are required for every adaptation call — square,
product image, and (when the card has one) logo.** Omitting the product image
or the logo (on a card that includes one) risks the model hallucinating that
element during recomposition.

**VERTICAL pass for the current batch:**

1. For every creative, upload media references via `media_upload`:
   - The square image (`sq_local_path`) — becomes the visual reference
   - The original product reference image (same file used in Sub-phase A Step 2,
     from `reference/products/Images/<Product to feature>/` per the Creative Brief) —
     always, for every creative
   - The primary logo file — only if `logo_included` is `true` for this
     creative
2. Build each creative's own bespoke vertical adaptation prompt following the
   **Vertical Adaptation Prompt** pattern:
   ```
   Adapt this ad (attached) to vertical 9:16 format.

   ## Elements to Preserve:
   - **Headline** "[this creative's exact headline]"
   - **Logo (only if logo_included is true)** Maintain the centred logo (attached) at the top of the image with clear space around it.
   - **Background** [restate this creative's background_description]
   - **Product** [product name] (attached) placed [restate this creative's product placement from the square]

   ## Elements to Change:
   - **Aspect Ratio**: Change the aspect ratio to vertical 9:16 format.
   - **More Spacing**: Give the design elements more breathing room to fill out the taller format.
   - **Composition**: Adapt the composition of the photograph to suit the taller frame.
   - **Sizing**: Increase the size of the logo and headline slightly to account for the increased vertical space.

   ## Rules:
   - **Danger Zone**: In vertical Meta ad placements, the bottom 25% of the frame (roughly the bottom 450 pixels of a 1920px-tall image) is blocked by the caption, audio track, and engagement bar. Ensure none of the key design elements (logo, headline, product) are placed within this zone.
   ```
   Omit the Logo bullet under "Elements to Preserve" entirely when `logo_included`
   is `false`.

   **Retain this exact prompt text** as `vt_prompt` in that creative's
   `phase3a-state.json` entry — logged to Sheets verbatim, not reconstructed.

   Once every creative in the batch has its prompt ready, fire
   `generate_image` for VERTICAL for all of them together — pass the square,
   the product image, and (when included) the logo as media references, model
   gpt_image_2, quality high, 1080×1920. Wait for all jobs to complete,
   suppress intermediate polling output.
3. For each creative, in turn: download → save to
   `outputs/generated-assets/[name]-VT_[DD-MM-YYYY].png`, upload to Drive
   (same folder as squares), capture the Drive link.

**HORIZONTAL pass for the same batch (immediately after the vertical pass):**

4. Reuse the square, product image, and (if included) logo references uploaded
   in step 1. Build each creative's own bespoke horizontal adaptation prompt
   following the **Horizontal Adaptation Prompt** pattern:
   ```
   Adapt this ad (attached) to horizontal 16:9 format.

   ## Elements to Preserve:
   - **Headline** "[this creative's exact headline]" centred vertically on the side adjacent to the main visual of the ad.
   - **Logo (only if logo_included is true)** Maintain the centred logo (attached) at the top of the image with clear space around it.
   - **Background** [restate this creative's background_description]
   - **Product** [product name] (attached) placed on the counter on the right side of the ad.

   ## Elements to Change:
   - **Aspect Ratio**: Change the aspect ratio to horizontal 16:9 format.
   - **More Spacing**: Give the design elements more breathing room to fill out the wider format.
   - **Composition**: Adapt the composition of the photograph to suit the wider frame.
   - **Sizing**: Increase the size of the logo and headline slightly to account for the increased horizontal space.
   ```
   The product/main visual always goes on the **right side** of the frame; the
   headline is always centred vertically on the **left side**, adjacent to it.
   Omit the Logo bullet when `logo_included` is `false`.

   **Retain this exact prompt text** as `hz_prompt` — logged verbatim.

   Fire `generate_image` for HORIZONTAL for the whole batch together — pass
   the square, product image, and (when included) logo, model gpt_image_2,
   quality high, 1920×1080. Wait for all jobs to complete, suppress
   intermediate polling output.
5. For each creative, in turn: download → save to
   `outputs/generated-assets/[name]-HZ_[DD-MM-YYYY].png`, upload to Drive,
   capture the Drive link, print: `✅ Creative [N] — [conceptName] — VT/HZ complete | Drive ✓`

Move to the next batch, repeating the vertical and horizontal passes, until all
N×2 adaptations exist.

### Sheets Logging — batch at the very end, after all N×2 VT/HZ assets are done

First check the column layout:
```bash
gws sheets spreadsheets values get \
  --params '{"spreadsheetId": "SPREADSHEET_ID", "range": "Creative Generations!1:1"}' 2>/dev/null
```

Then append all N×3 rows (SQ + VT + HZ for all N) in one batch, using the
correct column order. For each asset include: creative name, angle/concept,
Higgsfield prompt, Drive link, image copy (headline + subline from state), and
primary text (from state).

**The Higgsfield prompt column must always be the exact prompt actually sent to
`generate_image` — never a summary, paraphrase, or placeholder label:**
- **SQ rows:** `sq_prompt` from `phase3a-state.json`, verbatim.
- **VT rows:** `vt_prompt` from that creative's entry, verbatim.
- **HZ rows:** `hz_prompt` from that creative's entry, verbatim.

Do not write "Vertical adaptation" or any other shorthand — if you don't have
the exact prompt text captured, retrieve it rather than approximating.

### Creative Inventory

After Sheets logging, print a table of all N×3 assets grouped by concept, then
creative (A, B, C…), then SQ/VT/HZ per creative.

Then state:

> **Creative Generation complete. N creatives × 3 sizes = N×3 assets generated, uploaded to Google Drive, and logged to Google Sheets. Ready for the Phase 3.5 Generated Asset Review checkpoint.**

---

## Targeted Regeneration (invoked from the Phase 3.5 checkpoint)

`create-new-ads`'s Phase 3.5 checkpoint may come back with feedback on specific
creatives instead of a clean approval. Regenerate only those, not the whole batch.

**Feedback must specify which size(s) it applies to (SQ, VT, and/or HZ) —
only regenerate those.**

1. **Look up each flagged creative** in `outputs/generated-assets/phase3a-state.json`
   by `n` or `name` to retrieve its prior prompt fields.
2. **Apply the feedback as a direct correction** to the prompt for each
   size the feedback names — e.g. if the product doesn't match in the vertical,
   re-apply the reference-image instruction more explicitly in `vt_prompt`
   only; if it's the logo, adjust `logo_included` or the logo's prominence/clear-space
   wording in the affected size's prompt. Keep everything else unchanged unless the
   feedback says otherwise.
3. **Regenerate only the size(s) named in the feedback:**
   - **If the square is named:** regenerate the square, then also regenerate
     VT and HZ — they're built from the square as a reference, so a square
     change invalidates them. Skip VT/HZ regeneration only if the feedback
     explicitly says to leave a size alone.
   - **If only VT and/or HZ are named:** regenerate just those — do **not**
     regenerate the square. When firing `generate_image` for the
     regeneration, always upload the creative's existing `sq_local_path` (plus
     the logo) as `medias` references, exactly as in the original Sub-phase B
     pass, so the adaptation still has the square as its visual anchor.
4. **Overwrite, don't duplicate:** save over the same local filename(s),
   upload the new file(s) to Drive, update that creative's `phase3a-state.json`
   entry in place, and update — never append — its existing Sheet row(s),
   matching by creative name.
5. Report back just the regenerated creatives (same summary-line format) so
   the checkpoint can re-present only what changed.

---

## Generation Standards Checklist

Run this checklist before calling `generate_image` for **every size**:

**⛔ HARD STOP — do not call `generate_image` until #1 passes:**

1. ✅ **If this card's brief specifies a logo, confirm the primary logo file
   has been uploaded via `media_upload` and its returned ID is in the `medias`
   array** — not just described in the prompt text. This is the most common
   failure point. **If the brief specifies no logo for this card, confirm none
   was added.**

**Then verify:**

2. **Brand primary colour (hex from Brand Assets) referenced** — logo strip, headline colour, CTA bar, or a design element
3. **Fonts named correctly, headline font always described** — the headline font named plus its verbatim description from Brand Assets (`"in [headline font] font ([description])"`) for the headline; the body font (with description only if Brand Assets gives one) for every other text overlay (subline, badge, additional copy). Confirm neither role's phrasing bleeds into the other's, and no other serif/script/decorative font appears.
4. **Headline matches the brief card** — exact wording
5. **All copy elements from the brief card are included, and none invented** — subline, social proof, additional copy; no badges/claims not in the brief or grounded in `context/`
6. **Social proof appears exactly once (or not at all, if this card has none)** — named at a single position when present. Check the generated image before accepting — regenerate if it appears twice
7. **Prompt follows the Branded Paid Social Ad template** (the `## Design Layout:` / `## Visual Style:` markdown structure) from the prompt engineering reference
8. **Vertical (9:16) only — composition check:** confirm the bottom ~25% (~450px of a 1920px-tall image) "Danger Zone" is clear of key design elements, and the prompt points to the square's layout as reference. Regenerate if cramped or text/logo overlaps the bottom zone
9. **Product fidelity check:** confirm the prompt points the model at the attached reference image (e.g. "the [product type] shown in the images attached") rather than re-describing packaging/label from scratch. Visually compare the generated product against the reference — regenerate if it doesn't match
10. **Sectioning check:** confirm the headline/badge are described as overlaid directly on the continuous photographic background, not inside a separate solid-colour panel/band. Regenerate if the output shows a distinct colour-block section splitting photo from text.
11. **Grounded-props check:** when a grounded look is intended (lifestyle scenes, or a product-focus card choosing a clean grounded style), confirm no loose product pieces float, and that any override from the Product photography quirks (e.g. "Remove the floating pieces next to the pack") actually worked — regenerate if they still appear. Floating/tumbling pieces remain acceptable when a stylized card intentionally wants that dynamic look.
12. **Logo clear-space check (cards with a logo only):** confirm the logo has visible open margin in the prompt and the generated image, centred at the top — not cramped against the frame edge or crowded by other elements.
13. **Badge variety check (run once per batch, not per creative):** confirm the batch isn't defaulting every badge to the same orange rectangle — treatments should vary across creatives per the Visual Variety menu, and roughly half the batch should have no on-image badge at all.
14. **Logo frequency check (run once per batch, not per creative):** confirm roughly 3 in 4 creatives (~75%) include the logo — not all of them, and not none.
15. **USP icon row check (any creative using USP/benefit icons):** confirm the prompt renders the icons as a group of 3–4 in a single horizontal row, each paired with a short grounded benefit label, all in one shared color variant, with no background box — never a single lone/unlabeled icon in a corner. If the generated image shows an isolated icon with no label and no group, regenerate with the full row+label pattern.

---

## Naming Each Creative

```
[Concept]-[Variant]-[Size]_[DD-MM-YYYY]
```

Where `[Size]` is one of: `SQ` (square), `VT` (vertical), `HZ` (horizontal). The
concept and variant come directly from the Creative Brief card's `Creative` field.

Examples:
- `MythBreak-NewCustomer-A-SQ_06-07-2026`
- `MythBreak-NewCustomer-A-VT_06-07-2026`
- `TrustSeal-Certification-B-HZ_06-07-2026`

This name becomes the ad name in Meta Ads Manager, so the size suffix helps the
team quickly identify which asset is which during review.
