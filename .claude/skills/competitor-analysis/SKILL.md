---
name: competitor-analysis
description: >
  Analyses curated ad inspiration in reference/ad-inspiration/ — competitor ads
  the user has hand-picked plus the brand's own past winners — to extract winning
  creative concepts, hooks, angles, copy frameworks, and visual formats for
  the brand's Meta ad campaigns. Invoke this skill for Phase 1 of the Create New
  Ads workflow, or any time you need creative intelligence to inform strategy.
  Brand context is read on-demand from context/.
---

# Ad Inspiration Analysis — Creative Intelligence

Act as an **elite creative strategist and creative intelligence analyst**. Every
image in `reference/ad-inspiration/` has been hand-picked by the user — either a
competitor/cross-category ad they found genuinely inspiring, or one of the
brand's own past successful ads. This is high-signal, curated input. Study each one
closely, extract the patterns that make it work, and translate those learnings
into concrete creative concepts for **the brand** (named in `CLAUDE.md`'s Brand
Assets section, referred to below as "the brand").

---

## Step 1 — List Local Files & Check for Duplicates

Read `reference/gws-config.json` for the `ad_inspiration_folder_id` and `spreadsheet_id`.

**1. List local files:**
```bash
ls "reference/ad-inspiration/" 2>/dev/null
```

**2. Fetch existing filenames from column A of the "Ad Inspiration" tab to build a
deduplication list:**
```bash
gws sheets spreadsheets values get --params '{"spreadsheetId": "SHEET_ID", "range": "Ad Inspiration!A:A"}' 2>/dev/null
```

Only files not already in column A need analysis — these are the new additions
since the last run. If every local file is already logged, state that the folder
has no new inspiration and skip to re-reading the existing "Ad Inspiration" tab
rows (Step 3) to refresh the synthesis with full context.

---

## Step 2 — Visually Analyse Every New Image

For each local file whose name is NOT already in column A, read it directly with
the Read tool — no download needed, it's already local:

```
Read("reference/ad-inspiration/filename.jpg")
```

This is the most important part of this phase — spend real effort here. For every
image, extract:

- **Brand** — whose ad is this? Record the actual advertiser's name. If it's one
  of the brand's own past ads (its product, logo, or branding, or a filename
  prefixed with the brand name), record the brand name as-is. Those are proven
  winners worth studying especially closely
- **Hook / headline** — the opening line or copy that grabs attention first
- **Angle** — the core persuasion strategy (social proof, problem/solution,
  transformation, authority, price/value, lifestyle aspiration, urgency/scarcity)
- **Image copy** — all text on the creative (headline, subheadline, bullets, badges)
- **Visual format** — lifestyle photo, product-only, text-overlay dominant,
  before/after, UGC-style, illustrated, etc.
- **Composition** — hero element, foreground/background relationship, use of space
- **Colour treatment** — palette, warm/cool, contrast level
- **Logo treatment** — position, size, prominence
- **Mood** — the emotional register the image conveys

**This is non-negotiable** — copy alone is insufficient. The creative intelligence
is in how the image is composed, what it foregrounds, and what visual language it
uses. You cannot translate a winning concept to the brand without having seen and
understood the actual visual.

For each new file, also handle logging immediately after analysing it:

- Upload to the Ad Inspiration Drive folder:
  ```bash
  gws drive files create \
    --json '{"name": "FILENAME", "parents": ["AD_INSPIRATION_FOLDER_ID"]}' \
    --upload "reference/ad-inspiration/FILENAME" 2>/dev/null
  ```
- Capture the returned file `id`, construct the Drive link:
  `https://drive.google.com/file/d/FILE_ID/view`
- Append one row to the "Ad Inspiration" tab (columns A–F):
  ```bash
  gws sheets spreadsheets values append \
    --params '{"spreadsheetId": "SHEET_ID", "range": "Ad Inspiration!A:F", "valueInputOption": "USER_ENTERED"}' \
    --json '{"values": [["FILENAME", "BRAND", "CONCEPT", "IMAGE COPY", "DRIVE LINK", "IDEAS FOR OUR BRAND"]]}' 2>/dev/null
  ```

| Col | Field | What to put |
|---|---|---|
| A | Filename | Local filename (used for deduplication) |
| B | Brand | Advertiser's brand name (the brand's own name for its past ads) |
| C | Concept | The core creative concept / hook / angle |
| D | Image Copy | Text visible directly on the creative |
| E | Drive Link | `https://drive.google.com/file/d/FILE_ID/view` |
| F | Ideas for Our Brand | How this pattern could be translated for the brand |

---

## Step 3 — Synthesis & Brand Translation

After analysing every new image, re-read the existing rows in the "Ad Inspiration"
tab (`Ad Inspiration!A:F`) so your synthesis covers the whole curated set, not just
what's new this run — then do the deeper creative work.

### Pattern Recognition

Identify recurring patterns across the strongest ads in the set:
- Which hooks appear repeatedly, whether from competitors or the brand's own past winners?
- Which visual formats dominate among the standout pieces?
- Which angles suit the brand's category best (see `context/business-info.md`)?
- If the set includes the brand's own past ads, what do they tell you about what
  already works — and how can those be extended or remixed rather than repeated?

### Brand Translation

If these files are already in context from earlier in this session, reuse them
rather than re-reading — only read what's missing.

**Before writing any hook lines or concept descriptions, read these to
ground copy in real customer language:**
- `context/avatar_sheet.md` — customer avatars, exact language and phrases
- `context/necessary-beliefs.md` — the belief sequence each ad must advance
- `context/offer-brief.md` — USPs, offer structure, positioning, objections
- `context/customer-deep-research-report.md` — language patterns and what resonates

**Off-limits angles — discard any concept that leads with these, even if the
inspiration ad itself ran it successfully:**
- Customer service / human support messaging
- Generic delivery/returns claims as the primary hook
- Any baseline service-quality claim any ecommerce brand could make

These create zero desire and zero curiosity — they belong on product pages, not
in paid image ads. **Also check `context/offer-brief.md`'s *Off-Limits Claims &
Compliance Notes*** for this brand's own off-limits list, plus its *Stage of
Sophistication* notes, and apply both.

**For each remaining strong pattern, answer: "How would we do this for our brand?"**
Think through:
- Does this angle create emotional desire or genuine curiosity?
- What product/flavor would best fit this concept?
- What would the hook line be, in the brand's voice?
- What visual treatment would bring it to life using the brand's product imagery (`reference/products/Images/`)?

Produce a minimum of **10 distinct translated creative concepts**, each with:
- Concept name / working title
- Source inspiration (which image / brand / pattern inspired it)
- The angle it uses and the belief/objection it addresses
- A draft hook line for the brand
- Recommended visual format
- Recommended CTA

Aim for variety — cover different formats (lifestyle, product-led, review-based,
before/after, value/offer, authority), different emotional registers, and different
funnel positions (awareness vs. consideration vs. intent).

---

## Output Format

Produce an **Ad Inspiration Intelligence Report**:

1. **Inspiration Inventory** — table of every image analysed this run (filename,
   brand, hook/concept, visual format, copy notes)
2. **Top Patterns** — the 5–7 strongest recurring patterns, with examples drawn
   from specific images
3. **Brand Creative Concepts** — the 10+ translated concepts in full detail
4. **Priority Concepts** — flag 3–5 highest-priority concepts for this batch, with
   a one-sentence rationale each

After logging is complete, state:

> **Ad Inspiration Intelligence Report complete. [N] new ads analysed and logged to Google Sheets ([X] already logged, skipped). Ready to proceed to Phase 2: Creative Strategy.**
