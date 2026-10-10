# Meta Ads Creative Workspace for Claude Code

An end-to-end system that turns Claude Code into your static-ad creative team. It studies winning ads, writes a creative strategy grounded in your customer research, generates on-brand image ads in every Meta placement size, and builds them into a **paused** campaign in Meta Ads Manager, ready for you to review.

```
 Ad inspiration ──► Creative strategy ──► ✋ You approve the brief
                                                  │
 Paused Meta campaign ◄── ✋ You approve the images ◄── Image generation (SQ / VT / HZ)
```

> ⚠️ **Nothing ever goes live automatically.** Every campaign, ad set and ad is created **PAUSED**. A human has to activate it in Ads Manager before a single cent is spent.

---

## What's inside

| Phase | Skill | What it does |
|---|---|---|
| 1 | `competitor-analysis` | Reads every new ad in `reference/ad-inspiration/`, analyses the visual + copy, logs it to Google Sheets, and turns the patterns into 10+ concepts for your brand |
| 2 | `creative-strategy` | Picks 5–6 concepts and writes a 20-card Creative Brief (headline, primary text, product, visual direction, logo/badge decisions), scored against a 5-principle static-ad framework |
| ✋ | Brief review | You keep, cut or edit cards (`keep 1-12, change #4's headline to "…"`) |
| 3 | `creative-generation` | Generates each approved card with GPT Image 2 via Higgsfield in square (1:1), vertical (9:16) and horizontal (16:9), then uploads to Drive and logs to Sheets |
| ✋ | Image review | You look at every image and request targeted regenerations |
| 4 | `meta-ads-campaign-setup` | Builds an ABO testing campaign (one ad set per concept), uploads all three sizes per ad with placement-specific mapping, and writes 5 primary texts + 5 headlines per ad. **All PAUSED.** |

Also included:
- **`/onboard`**: an interactive setup that configures everything for your brand
- **`/prime`**: loads your context at the start of each session
- **`/create-plan`** + **`/implement`**: plan and make changes to the workspace itself
- A **starter swipe file** of strong static ads in `reference/ad-inspiration/`

---

## Prerequisites

| You need | Why | Notes |
|---|---|---|
| [Claude Code](https://claude.com/claude-code) | Runs the whole system | A paid Claude plan |
| **Higgsfield connector** | Image generation (`gpt_image_2`) | Connect in claude.ai → Settings → Connectors. Uses Higgsfield credits: a full batch is 20 creatives × 3 sizes = up to 60 high-quality generations, plus any regenerations |
| **Meta Ads connector** | Creates campaigns, ad sets and ads | Connect in claude.ai → Settings → Connectors |
| **Meta System User access token** | Uploads images to your ad account | Permissions `ads_management` + `ads_read`. `/onboard` walks you through it |
| **Google Workspace CLI (`gws`)** | Logs inspiration + creatives to Google Sheets, uploads images to Drive | See [googleworkspace/cli](https://github.com/googleworkspace/cli), then authenticate with your Google account |
| A Meta ad account, Pixel and Facebook Page | Where the campaign gets built | You'll need their IDs |

---

## Quick start

1. **Download / clone** this repo and open the folder in Claude Code.
2. **Run `/onboard`.** Claude interviews you (brand, colours, fonts, products, USPs, proof, Meta IDs, Google Sheet) and fills in every config file. Give it your website URL and it will pre-fill much of this for you.
3. **Add your assets:**
   - `reference/design-assets/`: your full logo lockup (icon + wordmark, transparent PNG) plus any icons, backgrounds or press logos. See the folder's README.
   - `reference/products/Images/`: one folder per product with packshots and lifestyle images. See the folder's README.
   - `reference/ad-inspiration/`: add your own picks (competitor ads, your past winners) next to the starter set.
4. **Start a fresh session**, run `/prime`, and say: **"let's make some new ads."**

---

## Where everything lives

```
CLAUDE.md                        ← Brand Assets (colours, fonts, logo) + workspace guide
context/
  business-info.md               ← who you are, products, proof figures
  personal-info.md               ← your role
  strategy.md                    ← goals and priorities
  offer-brief.md                 ← ⭐ USPs, CTAs, social proof, objections, off-limits claims
  avatar_sheet.md                ← customer avatars + verbatim language
  necessary-beliefs.md           ← the belief sequence your ads must build
  customer-deep-research-report.md
reference/
  meta-ads-config.json           ← ad account, pixel, page, budget, countries, landing pages
  gws-config.json                ← Google Sheet + Drive folder IDs
  ad-inspiration/                ← swipe file
  design-assets/                 ← logo, icons, backgrounds
  products/Images/               ← product photography, one folder per product
outputs/generated-assets/        ← the brief, state file and generated images (git-ignored)
.claude/settings.local.json      ← your META_ACCESS_TOKEN (git-ignored, never commit it)
```

**The golden rule:** the skills only ever use claims that are written in `context/`. If a USP, certification, review count or press mention isn't in `offer-brief.md` or `business-info.md`, it won't appear in an ad. The more accurate and specific those files are, the better the ads.

---

## Google Sheet layout

`/onboard` can create this for you. If you set it up by hand, make one spreadsheet with two tabs and these header rows:

**Ad Inspiration** (columns A–F)

| Filename | Brand | Concept | Image Copy | Drive Link | Ideas for Our Brand |
|---|---|---|---|---|---|

**Creative Generations**

| Creative Name | Angle/Concept | Higgsfield Prompt | Drive Link | Copy | Primary Text |
|---|---|---|---|---|---|

Then put the spreadsheet ID and two Drive folder IDs (generated assets, ad inspiration) into `reference/gws-config.json`.

---

## Optional: shell aliases

Add to `~/.zshrc` (or `~/.bashrc`), then run `source ~/.zshrc`:

```bash
alias cs='claude "/prime"'                                   # Claude Safe: asks before acting
alias cr='claude --dangerously-skip-permissions "/prime"'    # Claude Run: no permission prompts
```

- **`cs`** gives you oversight. It's good for unfamiliar tasks or for learning what Claude is doing.
- **`cr`** gives you speed for familiar workflows. Use it with care: Claude won't ask before running commands.

---

## Customising

- **Change the rules:** the skills are plain Markdown in `.claude/skills/`. Edit them directly, or run `/create-plan <change>` then `/implement` so `CLAUDE.md` stays in sync.
- **Batch size, logo frequency, badge frequency:** set in `creative-strategy` and `static-ads-strategy`.
- **Budget, countries, CTA:** `reference/meta-ads-config.json`.
- **Image model, sizes, prompt templates:** `creative-generation` and `.claude/skills/image-prompting/prompt-engineering.md`.

---

## Notes

- The ads in `reference/ad-inspiration/` belong to their respective brands. They're included only as reference for creative analysis, so don't reuse them in your own ads.
- AI-generated product shots can drift from the real packaging. That's why the image-review checkpoint exists: always check product accuracy, spelling and logo rendering before approving.
- You're responsible for making sure your ads comply with Meta's advertising policies and the regulations in your category.
