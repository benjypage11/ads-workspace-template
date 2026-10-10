---
name: creative-strategy
description: >
  Acts as creative director for the brand's Meta ad batches. Reads ad inspiration
  intelligence from Google Sheets, all context/ brand files, and the
  static-ads-strategy framework, then selects 5–6 concepts and writes a
  structured Creative Brief with 20 ready-to-execute creative cards. Invoke for
  Phase 2 of the Create New Ads workflow. Always run after competitor-analysis
  and before creative-generation.
---

# Creative Strategy

## Your Role

You are an **elite creative director** for the brand named in `CLAUDE.md`'s
Brand Assets section ("the brand" below). Synthesise all available
intelligence — competitive data, customer research, brand context, and static
ad best practices — into a single, actionable Creative Brief that the image
generation phase can execute without needing to make any strategic decisions.

Nothing is generated in this phase. This phase ends with a document.

---

## Step 1 — Read All Intelligence Sources

Read every source below before writing a single concept.

### Google Sheets — Ad Inspiration Tab

Read the spreadsheet ID from `reference/gws-config.json`, then fetch all rows:

```bash
gws sheets spreadsheets values get \
  --params '{"spreadsheetId": "SPREADSHEET_ID", "range": "Ad Inspiration!A:F"}' 2>/dev/null
```

Extract: filename, brand, concept, image copy, and ideas for our brand. These are
the curated reference ads — competitor ads plus the brand's own past winners —
with full visual analysis already logged by Phase 1.

### Google Sheets — Creative Generations Tab (Past Headlines)

```bash
gws sheets spreadsheets values get \
  --params '{"spreadsheetId": "SPREADSHEET_ID", "range": "Creative Generations!1:1"}' 2>/dev/null
```

Confirm which column is "Copy" from the header row, then fetch it in full:

```bash
gws sheets spreadsheets values get \
  --params '{"spreadsheetId": "SPREADSHEET_ID", "range": "Creative Generations!<Copy column>:<Copy column>"}' 2>/dev/null
```

Each cell holds `"Headline | Subline"`. Split on `|`, take the first segment
(trimmed) from every row — this builds a **Past Headlines** list spanning every
batch ever logged. Carry this list into Step 4.

### context/ Files — Brand, Customer, and USPs

If already in context from earlier this session, reuse rather than re-read.

Read:
- `context/avatar_sheet.md` — customer avatars, exact phrases and fears
- `context/necessary-beliefs.md` — the belief sequence each ad must advance
- `context/offer-brief.md` — Approved USPs, Approved CTA Sentences, Social Proof
  Assets, Objections & Creative Responses, Off-Limits Claims, Stage of
  Sophistication, plus offer structure and positioning
- `context/customer-deep-research-report.md` — language patterns and what resonates
- `context/business-info.md` — products, proof figures, press
- `CLAUDE.md`'s **Brand Assets** section — colors, headline/body fonts, primary logo file, logo rules

If `context/offer-brief.md` lists curated, attributed customer quotes, use them
**sparingly** — for a small handful of creatives in a dedicated social-proof
concept, never scattered across the batch, and never with the reviewer's
name/initials in the on-image copy. If none are listed, use only the aggregate
proof stats documented in the offer brief's *Social Proof Assets* table. Never
fabricate an individual quote.

### Static Ads Strategy Framework

Read `.claude/skills/static-ads-strategy/SKILL.md` in full. Contains:
- The five principles every static must pass (skimmability, objection handling,
  social proof, scroll-stopping imagery, copy simplicity)
- Off-limits angles that must never lead a concept
- How each principle applies to the brand

### Products Eligible for This Batch

`reference/products/Images/` is the full, authoritative, exhaustive list of what
can be advertised this batch — nothing outside it may be assigned to a creative.

```bash
ls "reference/products/Images/"
```

This returns one folder per product/variant (see `reference/products/README.md`)
— **always use the exact
folder name string this listing returns** when referencing a product later
(e.g. in a brief card's `Product to feature` field) — never retype or guess it.

There are no per-product SKUs or spec `.md` files — the folder name *is* the
product identifier. For each folder you plan to use:

```bash
ls "reference/products/Images/<exact folder name>/"
```

to see its available images. Source specs/claims (dosage, materials,
certification, ingredients, variants) from `context/offer-brief.md` and
`context/business-info.md` instead of a per-product file. Also read the
*Product photography quirks* section of `reference/products/README.md`.

### Design Assets Available for This Batch

`reference/design-assets/` holds every non-product on-brand visual asset available —
not just products. List it before drafting any cards:

```bash
ls "reference/design-assets/"
```

Read `reference/design-assets/README.md` for what each kind of asset is for.
Typical categories:
- **Primary logo**: the file named in `CLAUDE.md`'s Brand Assets (the only
  logo ever used as the primary AI-generated-creative logo, on the cards that
  include one). Icon-only marks are never used as the primary logo, and
  secondary marks (checkmark/seal) only illustrate a specific claim.
- **Background textures**: often matched to a product color/flavor
- **USP/benefit icon sets** (often in several color variants). **These are only
  ever used as a themed group of 3–4, never as a single lone icon accent.** When
  a brief card uses them, it must name all 3–4 icons chosen, each paired with a
  short grounded benefit label (sourced from `context/offer-brief.md` or
  `context/business-info.md` — never invented), and specify that all icons
  share one color variant, matched to the featured product. See the `Visual
  direction` field guidance below for the exact phrasing to use in the brief
  card. A single icon dropped into a corner with no label is never acceptable,
  regardless of how "small" or "subtle" it's described.
- **Certification/trust icons**: paired with the matching proof claim
- **Press logos**: for "As Featured In" proof, only for publications listed
  in the offer brief's Social Proof Assets

Brief cards should reference specific assets from this inventory **by exact filename**
where relevant — a press-mention card should name which press logo, a certification
card should name the matching trust icon, a product card should name its matching
icon color variant — rather than describing a generic or invented graphic that doesn't
actually exist in the folder. The folder contents can change over time, so always
re-list it. If the folder only contains the logo, design cards around the
product imagery and typography alone.

---

## Step 2 — Select 5–6 Concepts

From the Ad Inspiration Intelligence Report's priority concepts and pattern
insights, select 5–6 concepts for this batch.

**Off-limits filter — discard any concept that leads with:**
- Customer service or human support messaging
- Generic delivery/returns claims as the primary hook
- Anything listed in `context/offer-brief.md`'s *Off-Limits Claims &
  Compliance Notes* (and, if the offer brief marks the category as Stage 4–5
  sophisticated, hype-style efficacy claims such as "see results fast!")

**No unverified product claims — ever.** Never write or imply a product fact
that isn't sourced from a `context/` file — this includes novelty/launch status
("new," "new-in," "just launched"). Unless a `context/` file explicitly marks a
product as new, these words must not appear in any brief card.

**Five-principle filter** — score each candidate against the five-question
rubric in `static-ads-strategy`'s "Evaluating a Creative Concept Against the
Framework" section (already read in Step 1 — no need to re-derive it).

Discard any concept that fails two or more principles. Revise and retest rather
than compromise — weak concepts waste generation credits and testing budget.

**Diversity filter — the final 5–6 concepts should collectively cover:**
- At least two different objections (from the offer brief's *Objections &
  Creative Responses* table — e.g. price, trust, fit/identity, efficacy skepticism)
- At least two different visual formats (lifestyle, product hero, before/after,
  social proof wall, problem/solution split)
- At least two different funnel positions (awareness vs. consideration vs. intent)

---

## Step 3 — Plan the 20 Creatives

Distribute 20 creatives across the 5–6 selected concepts (3–4 per concept).
Within each concept, each creative must differ on at least one axis:

- Different product/variant featured (only folders listed in
  `reference/products/Images/` are eligible)
- Different avatar/emotional register (one per avatar in `context/avatar_sheet.md`,
  e.g. a myth-break for a skeptical newcomer vs. value for a price-sensitive
  regular vs. permission for a non-traditional buyer)
- Different copy angle
- Different visual treatment (lifestyle-led vs. product-led vs. copy-led)

Assign each creative a variant letter (A, B, C, D) within its concept and a
descriptive name segment indicating the differentiating element.

---

## Step 4 — Write the Creative Brief Cards

Write one brief card per creative. Every card must be complete enough that the
generation phase can execute it without returning for clarification.

### Brief Card Format

```
---
Creative: [ConceptName-Treatment-Variant]
Concept: [Concept group name]
Objection addressed: [The single objection this creative dismantles]
Five principles: Skimmability ✅/❌ | Objection ✅/❌ | Social proof ✅/❌ | Scroll-stop ✅/❌ | Simplicity ✅/❌

COPY
Headline on image: [≤7 words — the hook overlaid on the creative]
Subline on image: [≤10 words — optional; omit if not needed]
Social proof element: [Exact wording from the offer brief's Social Proof Assets, e.g. "★★★★★ [N]+ reviews" / "[Certification]" — required for ~50% of cards in the batch per static-ads-strategy's Principle 3 (rotate the visual treatment across cards — see image-prompting's Visual Variety menu; use a graphic star element whenever the proof is review/rating-based). For the other ~50% of cards, write "None (image) — carried via primary text only" and include the same proof point in Primary text instead.]
Additional copy (optional): [Any supplementary element, e.g. a callout badge, price anchor line — omit if not applicable. Must be grounded in a context/ file — never an unverified claim like "new-in"]
Primary text:
  [Hook line]

  [1–2 supporting sentences]

  [emoji] [USP 1]
  [emoji] [USP 2]
  [emoji] [USP 3]

  [CTA sentence]

PRODUCTION
Product to feature: [Exact folder name from reference/products/Images/]
Image type preferred: [packaging/hero shot / lifestyle or ingredient-context shot / generated scene]
Logo: [Yes — top-center, clear space / No — omit for this card. Assign to ~3 in 4 cards across the batch (mirroring the ~1-in-2 badge-frequency rule below) — see "Logo Frequency" below.]
Visual direction: [Composition, setting, colour treatment, mood, specific layout notes. Must state the scene mode explicitly as either "Stylized/product-focus" (clean backdrop, no realistic setting — floating/tumbling product pieces allowed) or "Lifestyle/realistic" (kitchen counter, gym, desk, hand-holding, etc. — any loose product pieces must be grounded: resting on a surface, in a hand, in a bowl, never floating). This flag is what creative-generation uses to decide whether floating props are permitted for this card. If this card uses USP/benefit icons from reference/design-assets/, spell out all 3–4 icons by exact filename, each with its short grounded benefit label, and the single shared color variant — e.g. "Four icon+text USP callouts in a horizontal row, no background box: [icon-1] beside '[Benefit 1]', [icon-2] beside '[Benefit 2]', [icon-3] beside '[Benefit 3]', [icon-4] beside '[Benefit 4]' — all four in their [color] variant." Never describe a single icon accent with no label.]
---
```

### Copy Standards for Every Card

**Headline on image:** Written in the customer's own language (from
`context/avatar_sheet.md`). Addresses a specific desire or fear — not a product
feature. ≤7 words.

> ⚠️ **Never lead a headline with a technical spec, dose, or ingredient number**
> (e.g. "500mg per capsule," "18-gauge steel," "[Ingredient]® [form]") — these
> describe the *product*, not the *outcome* it delivers. Even when accurate and
> sourced from `context/offer-brief.md`, they belong in the Production section's
> `Visual direction` or in supporting copy — never the hook.
> ❌ "500mg of [Branded Ingredient]® per capsule."
> ✅ "Finally, [category] you don't have to force down."
> ✅ "The Only [Product] That [Unexpected Benefit]"

> Write every headline as new wording of your own, checked against the Past
> Headlines list from Step 1 and the Ad Inspiration tab's Image Copy column —
> study material for tone and pattern only, never source text. If a draft
> headline matches or closely echoes either source, write a fresh one.

**Primary text structure:**
```
[Hook — one sentence, attention-grabbing, addresses avatar desire or fear]

[1–2 supporting sentences — emotional or contextual, not feature-listing]

[emoji] [USP 1]
[emoji] [USP 2]
[emoji] [USP 3]

[CTA — one of the offer brief's Approved CTA Sentences]
```

> **Use an emoji that matches each USP's meaning — never a generic ✅ checkmark
> for all three.** E.g.:
> ```
> 🧪 Third-party tested for purity
> ⭐️ [N]+ five-star reviews
> 🌱 [Ingredient/material benefit]
> ```

**USPs to rotate (use 3 per card, vary across the batch):** the *Approved
USPs* list in `context/offer-brief.md`, with their emoji. Use only those, and
don't introduce new ones.

**CTA sentences to rotate:** the *Approved CTA Sentences* list in
`context/offer-brief.md`. Evergreen only: no time-limited promo codes unless
the user asks for them in this batch.

### Logo — Full-Width Only, Top-Center, ~3-in-4 Frequency

See `CLAUDE.md`'s **Brand Assets** section — the canonical source for the
file/position rule. Update there first if the rule ever changes, and this
skill will follow automatically.

When a card includes the logo, it is always the primary logo file named in
`CLAUDE.md`'s Brand Assets, centred at the top of the image with clear open
space around it — never another file, never another position. Whether a given
card includes the logo at all is a per-card creative decision, **not
automatic**: assign it to roughly **3 in 4 cards (75%)** across the batch,
mirroring the existing ~1-in-2 badge-frequency rule (`static-ads-strategy`'s
Principle 3). Vary which cards omit it based on composition/concept fit — e.g.
a copy-driven comparison chart or a clean editorial lifestyle shot often reads
better without it, relying on the product packaging's own branding.

---

### Before Finalizing — Confirm Every Headline Is New

Once all 20 cards are drafted, read back down the full list of headlines and
cross-check each against the Past Headlines list and the Ad Inspiration Image
Copy column one more time — a dedicated final gate, rather than relying on
getting it right while drafting each card.

---

## Output — The Creative Brief

Produce the Creative Brief as a structured document with all 20 cards, grouped
by concept. Begin each concept group with a one-paragraph summary (the angle,
the objection it handles, why it was selected from the competitive intelligence).

End with a **Concept Overview Table**:

| Concept | Variants | Objection addressed | Visual format |
|---|---|---|---|
| [Name] | A, B, C | [Objection] | [Format] |

Then state:

> **Creative Brief complete. 20 creatives across [N] concepts. Ready to pass to Phase 3: Creative Generation.**
