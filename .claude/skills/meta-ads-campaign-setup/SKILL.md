---
name: meta-ads-campaign-setup
description: "Sets up the brand's Meta Ads campaign (ABO) in Meta Ads Manager using the Meta connector, using the account details in reference/meta-ads-config.json. All campaigns and ads are created in PAUSED/DRAFT status — nothing is activated or made live. Invoke this skill for Phase 4 of the Create New Ads workflow, or any time you need to build a new campaign structure for review. Always run this after creative generation so all assets are ready before setup begins."
---

# Meta Ads Campaign Setup

## ⚠️ CRITICAL: DO NOT ACTIVATE ANY ADS ⚠️

**Every campaign, ad set, and ad created by this skill must be set to PAUSED or
DRAFT status. Under no circumstances should any campaign, ad set, or ad be
activated, made live, or allowed to spend budget. This is an absolute rule with
zero exceptions.**

The purpose of this skill is to build a complete campaign structure for
**human review and approval**. A real person must manually review and activate
everything before any spend occurs. State this clearly in your summary output.

---

## Account Details — Read From Config

All account-specific values live in **`reference/meta-ads-config.json`**. Read it
first and use its values throughout. Do not look them up some other way and do not
prompt the user for them:

| Config key | Used for |
|---|---|
| `ad_account_id` | Every Meta call; the Graph API image-upload URL (`act_<ad_account_id>`) |
| `pixel_id` | Ad set conversion tracking |
| `page_id` | `object_story_spec` on every ad |
| `conversion_event` | Ad set optimisation event (default `Purchase`) |
| `attribution` | Ad set attribution window (default 7-day click, 1-day view) |
| `currency`, `currency_symbol` | Budget display; budgets are sent to the API in the currency's minor unit |
| `budget_per_creative_daily` | Daily ad-set budget per creative |
| `countries` | Geo targeting |
| `website_domain`, `display_link`, `landing_pages` | Ad destination URLs |
| `campaign_name_prefix` | Campaign naming |
| `cta_type` | Call to action (default `SHOP_NOW`) |

**If the file is missing, or any of `ad_account_id`, `pixel_id`, `page_id`,
`website_domain` still contains a `{{…}}` placeholder, stop.** Tell the user to
run `/onboard` (or fill in the file) before Phase 4. Never guess or invent an ID.

---

## Step 0 — Read Creative Data from Google Sheets

**All campaign structure, ad copy, and asset information comes from the
Creative Generations tab. Read it first, before touching any Meta tools.**

Read `reference/gws-config.json` for the spreadsheet ID, then fetch all rows:

```bash
gws sheets spreadsheets values get \
  --params '{"spreadsheetId": "SPREADSHEET_ID", "range": "Creative Generations!1:500"}' 2>/dev/null
```

The first row is the header. Parse the column layout from it. The data you
need per row:

| Column | Field |
|---|---|
| Creative name | The full asset name (e.g., `MythBreak-NewCustomer-A-SQ_06-07-2026`) |
| Angle / concept | The concept group (e.g., `MythBreak-NewCustomer`) |
| Drive link | The Google Drive URL for this asset |
| Image copy / headline | The on-image headline text from the brief card |
| Primary text | The full primary text body for this creative |

**Filter to SQ (square) rows only for campaign structure purposes** — one SQ
row per creative gives you the creative name, concept grouping, and copy. VT
and HZ rows are additional size variants uploaded alongside the square for the
same ad, not separate ads. So if you have 20 creatives × 3 sizes = 60 rows, use
the 20 SQ rows to drive the ad structure, then upload all three local files
(SQ + VT + HZ) per creative in Step 4a.

**Group the creatives by concept** (the first segment(s) of the creative name,
or the Angle/Concept column). This grouping determines the ad set structure:
one ad set per concept.

---

## Step 1 — Confirm Ad Account

Use `ads_get_ad_accounts` to verify the account is accessible, then confirm
you're working against the `ad_account_id` from `reference/meta-ads-config.json`
for all subsequent calls. If it isn't in the accessible list, stop and tell the
user. Don't fall back to another account.

---

## Campaign Structure Overview

One **ABO Testing Campaign** with one ad set per concept. Each ad set contains
the 3–4 individual creatives for that concept as separate ads. This isolates
performance data per concept while keeping budgets predictable.

```
[campaign_name_prefix] - ABO Testing Campaign (PAUSED)
├── Ad Set: [ConceptA] | ABO | [DD/MM/YYYY] (PAUSED)
│   ├── Ad: ConceptA-A (PAUSED)
│   ├── Ad: ConceptA-B (PAUSED)
│   └── Ad: ConceptA-C (PAUSED)
├── Ad Set: [ConceptB] | ABO | [DD/MM/YYYY] (PAUSED)
│   ├── Ad: ConceptB-A (PAUSED)
│   └── ...
└── ...
```

---

## Step 2 — Create the Campaign

| Parameter | Value |
|---|---|
| Name | `[campaign_name_prefix] - ABO Testing - [Month Year] - Image Ads` |
| Buying type | Auction |
| Objective | Sales |
| Campaign spending limit | None |
| Budget type | Ad set budget (ABO) |
| Bid strategy | Highest volume or value (default) |
| Status | **PAUSED** |

Use `ads_create_campaign` with these parameters.

---

## Step 3 — Create Ad Sets (one per concept)

For each concept group from Step 0, create one ad set using `ads_create_ad_set`.

### Ad Set Naming

```
[ConceptName] | ABO | [DD/MM/YYYY]
```
Example: `MythBreak-NewCustomer | ABO | 06/07/2026`

### Ad Set Parameters

| Parameter | Value |
|---|---|
| Campaign | The campaign ID from Step 2 |
| Daily budget | `budget_per_creative_daily` × number of creatives in this concept, sent to the API in the currency's minor unit (e.g. budget 10, 3 creatives = 30/day = `3000` cents/pence) |
| Conversion location | Website |
| Performance goal | Maximise value of conversions |
| Pixel ID | `pixel_id` from config |
| Conversion event | `conversion_event` from config |
| Attribution | `attribution` from config |
| Schedule | Always on / no end date |
| Age targeting | Meta default (leave blank) |
| Interest / custom targeting | None — broad only |
| Audience suggestions | Off |
| Placements | Advantage+ Placements, **Audience Network excluded** (see below) |
| Status | **PAUSED** |

### Geographic Targeting — Applied to Every Ad Set

**Default: the `countries` list from config, broad, no exclusions.** No regional
exclusions unless a specific brief or the user calls for them.

```json
{
  "age_min": 18,
  "age_max": 65,
  "geo_locations": { "countries": [<countries from config>], "location_types": ["frequently_in", "home"] },
  "targeting_automation": { "advantage_audience": 1 },
  "publisher_platforms": ["facebook", "instagram"],
  "facebook_positions": ["feed", "marketplace", "right_hand_column", "story", "search", "instream_video"],
  "instagram_positions": ["stream", "story", "explore", "reels"]
}
```

> **Audience Network exclusion REQUIRES an explicit `publisher_platforms`
> list** — it is not excluded by default. List `publisher_platforms` explicitly
> as `["facebook", "instagram"]` (without `"audience_network"` and without
> `audience_network_positions`). Leaving `publisher_platforms` unset defaults
> to Advantage+ Placements, which DOES include Audience Network.
>
> **Do NOT include `"video_feeds"` in `facebook_positions`** — deprecated for
> the current API version; both `ads_create_ad_set` and `ads_update_entity`
> reject it (error_subcode `2490562`). Omit it; the remaining positions still
> give full Facebook coverage.

**If a future brief needs regional exclusions:** only some Meta geo-types are
`region`-type; others (e.g. county-level areas) require the undocumented
`medium_geo_areas` field inside `excluded_geo_locations`, not the public
`geo_locations` reference table. Pass **`key`-only objects** on write (e.g.
`{"key": "4082"}`) — the live `ads_create_ad_set` call rejects objects that also
include `name`/`country_code`, even though Meta's `/targetingsentencelines`
dry-run validator accepts them. Verify any exclusion by re-fetching the saved
ad set's `targeting` field (or running it through `/targetingsentencelines`)
before trusting it.

### Placements — Audience Network Must Be Off

Use Advantage+ Placements but manually deselect **Audience Network**. Verify
it is deselected on every ad set before saving.

---

## Step 4 — Upload Images to Meta and Create Ads

> **Note on `ads_create_creative`:** This tool is in gradual rollout and may not
> be available on your ad account (only Meta can unlock it). The proven
> solution used throughout this skill works on every account: `ads_create_ad`
> with **Placement Asset Customization (PAC)**: combine `object_story_spec`
> (page_id only) with `asset_feed_spec` + `asset_customization_rules` (Step
> 4c). This maps all three uploaded image hashes (SQ/VT/HZ) to the correct
> placements automatically — fully scripted, no manual thumbnail selection.

For each creative in the concept group:

### 4a — Upload all three size variants to Meta via curl

All three size variants are already saved locally in `outputs/generated-assets/`.
Upload directly to Meta's ad image library via the Graph API. Authentication
uses `$META_ACCESS_TOKEN`, a System User token that never expires, set up
once in `.claude/settings.local.json` (git-ignored, so it never gets committed).

**If `$META_ACCESS_TOKEN` is not set**, instruct the user to:
1. Go to Meta Business Suite → Settings → Users → System Users
2. Select (or create) an Admin System User → Generate New Token
3. Grant permissions: `ads_management`, `ads_read`
4. Assign the System User to the ad account (and Page) with full control
5. Copy the token into `.claude/settings.local.json`: `{"env": {"META_ACCESS_TOKEN": "token_here"}}`. **Never into `settings.json`**, which is committed to git
6. Restart Claude Code so the env var loads

**Upload command** (run for each file, sequentially — not in parallel):

```bash
AD_ACCOUNT_ID=$(python3 -c "import json; print(json.load(open('reference/meta-ads-config.json'))['ad_account_id'])")
HASH=$(curl -s -X POST \
  "https://graph.facebook.com/v21.0/act_${AD_ACCOUNT_ID}/adimages" \
  -H "Authorization: Bearer $META_ACCESS_TOKEN" \
  -F "filename=@outputs/generated-assets/[CreativeName]-SQ_[DD-MM-YYYY].png" \
  | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d['images'].values())[0]['hash'])")
echo "SQ hash: $HASH"
```

Run the same command for VT and HZ variants. **Capture all three hashes for
every creative** — all three are used programmatically in Step 4c via
`asset_customization_rules`, which maps each hash to its correct placement group.

| Size | File suffix | Dimensions |
|---|---|---|
| Square | `-SQ_` | 1080×1080 |
| Vertical | `-VT_` | 1080×1920 |
| Horizontal | `-HZ_` | 1920×1080 |

### 4b — Select the landing page

Use the `landing_pages` map in `reference/meta-ads-config.json` to pick the most
relevant destination for this creative's product/concept. Match on product
folder name or concept type, and fall back to `default`. If no mapping fits well,
browse `website_domain` (via `WebFetch`) for the best matching product or
collection page.

Always use a real, working URL on `website_domain`. Never make one up.

### 4c — Create the ad with Placement Asset Customization (PAC)

```json
{
  "object_story_spec": {"page_id": "{page_id from config}"},
  "asset_feed_spec": {
    "images": [
      {"hash": "{SQ_HASH}", "adlabels": [{"name": "img_sq"}]},
      {"hash": "{VT_HASH}", "adlabels": [{"name": "img_vt"}]},
      {"hash": "{HZ_HASH}", "adlabels": [{"name": "img_hz"}]}
    ],
    "bodies": [{"text": "{primary text}"}],
    "titles": [{"text": "{headline}"}],
    "link_urls": [{"website_url": "{landing_page}", "display_url": "{display_link from config}"}],
    "ad_formats": ["SINGLE_IMAGE"],
    "call_to_action_types": ["{cta_type from config}"],
    "asset_customization_rules": [
      {
        "customization_spec": {
          "publisher_platforms": ["facebook", "instagram"],
          "facebook_positions": ["feed", "marketplace", "right_hand_column"],
          "instagram_positions": ["stream"]
        },
        "image_label": {"name": "img_sq"}
      },
      {
        "customization_spec": {
          "publisher_platforms": ["facebook", "instagram", "messenger"],
          "facebook_positions": ["story"],
          "instagram_positions": ["story", "reels"],
          "messenger_positions": ["story"]
        },
        "image_label": {"name": "img_vt"}
      },
      {
        "customization_spec": {
          "publisher_platforms": ["facebook"],
          "facebook_positions": ["instream_video"]
        },
        "image_label": {"name": "img_hz"}
      }
    ]
  }
}
```

Use `ads_create_ad` with:

| Parameter | Value |
|---|---|
| Ad set | The ad set ID for this concept |
| Name | `[ConceptName]-[Variant]_[DD/MM/YYYY]` (see naming convention below) |
| Status | **PAUSED** |
| Display link | `display_link` from config |
| Personalised destinations | Off |

---

## Copy — Primary Text (5 Variations per Ad)

The Creative Generations tab contains a primary text column for each creative
— this is **variation 1**. Write 4 additional variations seeded from the same
brief, rotating the hook angle, USPs, and CTA sentence — same structure (hook →
supporting copy → 3 emoji-led USPs → CTA), same USP rotation, same
emoji-matching rule, and same CTA rotation that `creative-strategy`'s "Copy
Standards for Every Card" section defines. All 5 must target the same avatar
and concept.

---

## Copy — Headlines (5 Variations per Ad)

Write 5 ad-level headline variations per ad, distinct from the on-image
headline. Keep them under 40 characters. Focus on USPs and brand proof points.

Base the angle on the creative's concept from the Sheets data. Use only USPs and
proof points from `context/offer-brief.md` (*Approved USPs* and *Social Proof
Assets*). Never invent a claim. Examples of the right tone (illustrative only):

- `[Core benefit] in Every [Unit]`
- `[N]+ Five-Star Reviews`
- `Trusted by [N]+ Customers`
- `[Product] You Can Trust (And Actually Enjoy)`
- `[Sensory benefit], Works Like [Proof]`

---

## Ad Naming Convention

```
[ConceptName]-[Variant]_[DD/MM/YYYY]
```

The concept name and variant come directly from the Sheets creative name,
dropping the size suffix. Examples:
- `MythBreak-NewCustomer-A_06/07/2026`
- `TrustSeal-Certification-B_06/07/2026`

---

## Creative Enhancements — ⚠️ ALL MUST BE OFF ⚠️

**Check every enhancement on every ad and confirm all are disabled. Meta
silently re-enables these — check twice before saving each ad.**

- Site links: **OFF**
- Website highlights: **OFF**
- Promotions: **OFF**
- Products: **OFF**
- Music: **OFF** (if present)
- Image enhancements: **OFF**
- Automatic placements enhancements: **OFF**
- Advantage+ creative: **OFF**

After setting them to off, check again before saving.

---

## Final Verification Checklist

Before completing setup, confirm every item:

- [ ] Campaign status: PAUSED
- [ ] All ad sets: PAUSED
- [ ] All ads: PAUSED
- [ ] Geographic targeting matches the brief (default: `countries` from config, no exclusions)
- [ ] Audience Network excluded from placements on every ad set
- [ ] All creative enhancements: OFF on every ad
- [ ] CTA: `cta_type` from config on every ad
- [ ] 5 primary text variations on every ad
- [ ] 5 headline variations on every ad
- [ ] Ad names follow naming convention with date suffix
- [ ] Personalised destinations: OFF
- [ ] Display link: `display_link` from config on every ad
- [ ] Landing page: a real, working URL on `website_domain` on every ad

---

## Output Summary

After completing setup, produce a **Campaign Structure Summary**:

```
CAMPAIGN SETUP COMPLETE — AWAITING HUMAN REVIEW
================================================
⚠️  Everything is PAUSED. No spend will occur until manually activated.

ABO TESTING CAMPAIGN
  Campaign ID:   [ID]
  Campaign Name: [Name]
  Status:        PAUSED

AD SETS ([N] total — one per concept):
  [ConceptName] | ABO | [date]
    ID: [ID] | Budget: [currency_symbol][X]/day | Ads: [N] | Status: PAUSED
    Ads:
      [AdName] — ID: [ID] — PAUSED
      ...
  ...

TOTAL ADS CREATED: [N]

ALL CAMPAIGNS, AD SETS, AND ADS ARE PAUSED.
Nothing is live. Manual review and activation required before any spend occurs.
```
