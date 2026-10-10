# Onboard

> One-time setup that configures this workspace for the user's brand. Interview the user, then write their answers into the right files. Safe to re-run: if a file is already filled in, show what's there and only change what the user wants to update.

## Variables

section: $ARGUMENTS (optional — e.g. `brand`, `offer`, `meta`, `google`. If given, run only that round, then the readiness check.)

---

## How to run this

- Start by explaining in 3–4 lines what's about to happen: six short rounds, about 15–30 minutes, and what they'll have at the end.
- Ask **one round at a time**, in plain conversation. Group questions so each round is one message, not twenty. `AskUserQuestion` is fine for genuine multiple-choice items (e.g. currency); free-text answers go in normal chat.
- If the user gives a website URL, use `WebFetch` on it (homepage, product pages, about page, reviews) to **pre-fill draft answers**, then ask them to confirm or correct. Don't make them type what you can read.
- **Never invent claims, numbers, certifications, press mentions or reviews.** If something isn't confirmed by the user or clearly stated on their own site, leave it out. Everything in `context/offer-brief.md` can end up in a live ad.
- Write each file at the end of its round, then show a short summary of what was written.
- Leave a `{{…}}` placeholder in place (don't delete it) for anything the user wants to skip, so the readiness check flags it.

---

## Round 1 — Business & role

Ask for: brand name, website, what they sell and who it's for, positioning/differentiation, sales channels, real proof figures (review count, customer count), genuine press coverage, their role, and their current goals for Meta ads (current vs. target ROAS, creative volume per week, share of revenue, hours spent).

Write:
- `context/business-info.md`
- `context/personal-info.md`
- `context/strategy.md`

## Round 2 — Brand identity

Ask for:
- Primary, secondary/light-background, background and text colours (hex). Offer to pull them from the website CSS if they don't know.
- **Headline font** name, plus a short visual description (e.g. "an elegant, tall-proportioned serif with moderate stroke contrast and regular-to-medium weight"). Explain why: image models have never seen custom fonts, and the description is what makes them render it correctly. Offer to write the description for them from a screenshot or the website.
- **Body font** name (description optional).
- **Logo:** ask them to put their full logo lockup (icon + wordmark, transparent PNG) in `reference/design-assets/`. Confirm the filename with `ls`. If they only have an icon-only mark, warn that AI generation needs the wordmark version.

Write: the **Brand Assets** table in `CLAUDE.md`. Replace every `{{…}}` in that section, and update the Setup status note if everything is filled.

## Round 3 — Products & offer

Ask for:
- The products to advertise, plus key specs, variants, pricing, subscription terms, guarantee and shipping
- **Approved USPs** (5–8), each with a matching emoji
- **Approved CTA sentences** (3–5, evergreen)
- **Social proof assets**: exact wording only, plus which design-asset file goes with each (if any)
- **Top objections** and how they'd answer each
- **Off-limits claims**: regulated health claims, legal sensitivities, words to avoid
- Market sophistication (how claim-fatigued is the category?)

Write: `context/offer-brief.md`. Fill all four sections marked *(used directly by the skills)* first, then as much of the strategic sections (big idea, UMP/UMS, awareness, etc.) as the user can give. Offer to draft the rest for their review.

Then offer to draft the research files. If they accept, use `WebSearch`/`WebFetch` on their reviews, competitor reviews, Reddit and Amazon to draft:
- `context/customer-deep-research-report.md` (focus on the **Language bank**: verbatim quotes only, with sources)
- `context/avatar_sheet.md` (3–5 avatars, from the research)
- `context/necessary-beliefs.md`

Tell them these drafts are a starting point and that real customer quotes beat anything synthesized.

Then ask them to add product photos to `reference/products/Images/<product-folder>/` per `reference/products/README.md`, and to note any photography quirks in that README (e.g. loose pieces baked into the packshot).

## Round 4 — Meta Ads account

Ask for:
- Ad account ID (digits only, no `act_`), Pixel ID, Facebook Page ID. Point them to Meta Business Settings → Accounts / Data sources.
- Currency and symbol, target countries, daily budget per creative (default 10)
- Conversion event (default Purchase) and attribution (default 7-day click, 1-day view)
- Website domain, display link, and landing pages: map each product folder (or concept type) to its URL, plus a default. Verify each URL loads with `WebFetch`.

Write: `reference/meta-ads-config.json`.

Then the access token. Walk them through creating a **System User** token (Business Settings → Users → System Users → Admin → Generate token with `ads_management` + `ads_read`; assign the system user to the ad account and Page). Ask them to paste it **into `.claude/settings.local.json` themselves**, as `{"env": {"META_ACCESS_TOKEN": "..."}}`, or offer to write that file for them. **Never write the token into `settings.json` or any other tracked file**, and don't echo it back in chat. Remind them to restart Claude Code afterwards so it loads.

Also confirm the **Meta Ads connector** is connected in Claude (it provides the `ads_*` tools). If it isn't, tell them to connect it via claude.ai → Settings → Connectors.

## Round 5 — Google Sheets & Drive

Check `gws` is installed (`which gws`). If it isn't, point them to the README's Google Workspace CLI section and continue.

Ask for (or offer to create):
- A Google Sheet with two tabs:
  - **Ad Inspiration** (A–F): `Filename | Brand | Concept | Image Copy | Drive Link | Ideas for Our Brand`
  - **Creative Generations**: `Creative Name | Angle/Concept | Higgsfield Prompt | Drive Link | Copy | Primary Text`
- A Drive folder for generated assets, and one for ad inspiration

If they'd like you to create these and `gws` is authenticated, create the sheet, tabs and header rows and the two folders, then capture their IDs.

Write: `reference/gws-config.json`.

## Round 6 — Readiness check

Run these checks and print a ✅/⚠️ checklist:

1. `grep -n "{{" CLAUDE.md context/*.md reference/*.json`: list every unfilled placeholder, grouped by file. Placeholders in the strategic sections of the offer brief are ⚠️ (nice to have). Placeholders in Brand Assets, the four *(used directly by the skills)* sections, `meta-ads-config.json` or `gws-config.json` are ❌ (they block the workflow).
2. The primary logo file named in Brand Assets exists in `reference/design-assets/`
3. `reference/products/Images/` has at least one product folder containing at least one image
4. `reference/ad-inspiration/` has images (a starter set ships with the template)
5. `META_ACCESS_TOKEN` is set (`[ -n "$META_ACCESS_TOKEN" ] && echo set`, without printing the value)
6. Connectors available: Higgsfield (`generate_image`, `media_upload`), Meta Ads (`ads_get_ad_accounts`)
7. `gws` installed and authenticated

Finish with what to do next: fix any ❌ items, then start a fresh session (`/prime`) and say **"let's make some new ads."** Remind them that the workflow only ever creates **PAUSED** campaigns, so nothing spends until they activate it in Ads Manager.
