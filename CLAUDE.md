# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## What This Is

This is a **Meta Ads Creative Workspace** for Claude Code. It's a structured environment where Claude acts as an agent assistant that researches ad inspiration, writes a creative strategy, generates static image ads (via the Higgsfield connector), and builds them into a **paused** Meta Ads Manager campaign for human review.

The user spins up fresh Claude Code sessions repeatedly, using `/prime` at the start of each to load essential context without bloat. A brand-new user runs `/onboard` once to configure the workspace for their brand.

**This file (CLAUDE.md) is the foundation.** It is automatically loaded at the start of every session. Keep it current — it is the single source of truth for how Claude should understand and operate within this workspace.

> **Setup status:** If the Brand Assets table below still contains `{{…}}` placeholders, the workspace hasn't been configured yet. Tell the user to run `/onboard` before running the ads workflow.

---

## The Claude-User Relationship

Claude operates as an **agent assistant** with access to the workspace folders, context files, commands, and outputs. The relationship is:

- **User**: Defines goals, provides context about their role/function, and directs work through commands
- **Claude**: Reads context, understands the user's objectives, executes commands, produces outputs, and maintains workspace consistency

Claude should always orient itself through `/prime` at session start, then act with full awareness of who the user is, what they're trying to achieve, and how this workspace supports that.

---

## Workspace Structure

```
.
├── CLAUDE.md                    # This file — core context + Brand Assets, always loaded
├── README.md                    # Setup guide for new users
├── .claude/
│   ├── commands/                # Slash commands
│   │   ├── onboard.md           # /onboard — one-time brand & account setup
│   │   ├── prime.md             # /prime — session initialization
│   │   ├── create-plan.md       # /create-plan — create implementation plans
│   │   └── implement.md         # /implement — execute plans
│   ├── skills/                  # The ads pipeline (see "Ads Workflow" below)
│   ├── settings.json            # Shared settings (no secrets)
│   └── settings.local.json      # Your secrets, e.g. META_ACCESS_TOKEN (git-ignored)
├── context/                     # Brand, customer, offer and strategy context. Read by /prime and the skills.
├── plans/                       # Implementation plans created by /create-plan
├── outputs/generated-assets/    # Creative briefs, state files and generated ad images
├── reference/
│   ├── ad-inspiration/          # Curated swipe file of static ads (Phase 1 input)
│   ├── design-assets/           # Logo, icons, backgrounds, press/trust logos
│   ├── products/Images/         # One folder per product — the products eligible for ads
│   ├── gws-config.json          # Google Sheet + Drive IDs
│   └── meta-ads-config.json     # Meta ad account, pixel, page, budget, targeting, landing pages
└── scripts/                     # Automation scripts (if applicable)
```

**Key directories and files:**

| Path | Purpose |
| --- | --- |
| `context/` | Who the user is, the business, strategy, offer brief (USPs, CTAs, proof, objections, off-limits claims), avatars, necessary beliefs, customer research. |
| `reference/ad-inspiration/` | Hand-picked reference ads analysed in Phase 1. |
| `reference/design-assets/` | Every non-product visual asset the image model may use. See its README. |
| `reference/products/Images/` | Product photography, one folder per product. See its README, including the *Product photography quirks* section. |
| `reference/meta-ads-config.json` | Everything account-specific that campaign setup needs. |
| `reference/gws-config.json` | The Google Sheet (Ad Inspiration + Creative Generations tabs) and Drive folders used for logging. |
| `outputs/` | Deliverables and generated creatives. |
| `plans/` | Detailed implementation plans. |

---

## Brand Assets

Canonical source for the brand's visual identity. Skills reference this section rather than repeating these values. Filled in by `/onboard`.

| Property | Value |
| --- | --- |
| Brand name | **{{BRAND_NAME}}** |
| Website | {{yourbrand.com}} |
| Primary color | `{{#HEX}}` ({{color name}}). Accent for headlines, badges, CTA bars, design elements. |
| Secondary / light background | `{{#HEX}}` |
| Background | `{{#HEX}}` |
| Text | `{{#HEX}}` |
| Font — Headline | **{{HEADLINE_FONT}}**, reserved exclusively for the main headline text on a creative. Never used for subline, badge, additional copy, or logo. In image-generation prompts, always name AND describe it together, verbatim: "in {{HEADLINE_FONT}} font ({{HEADLINE_FONT_DESCRIPTION}})." |
| Font — Body / Badge | **{{BODY_FONT}}**, used for every other rendered text element: subline, social-proof badge text, additional on-image copy. (Primary/caption text is not image-rendered, so it needs no font instruction.) Named alone in prompts unless it renders inconsistently (e.g. "in white {{BODY_FONT}} text"). |
| Primary logo file | `reference/design-assets/{{logo-full-width.png}}` |

> **Why fonts are described as well as named:** image models have never seen custom or licensed fonts. With the name alone, they guess, often rendering the wrong weight (e.g. an unwanted heavy/bold serif). A short visual description in parentheses ("an elegant, tall-proportioned serif with moderate stroke contrast and regular-to-medium weight") fixes this. Always describe the headline font. Add a description for the body font too if it ever renders inconsistently.

> **Two-font system:** headline font for the headline only, body font for everything else rendered in the image. Never mix these roles, and never introduce a third font.

**Logo: full-width lockup only, ~3-in-4 frequency.**

When a creative includes the logo (per the Creative Brief's per-card decision, typically **~3 in 4 creatives in a batch**, not every one), it is always the primary logo file above (the icon + wordmark lockup), centred at the top of the image with clear open space around it. Never move it to another corner or position, and never substitute a bare icon file. Some creatives run without a logo overlay and rely on the product packaging's own branding.

**Never upload an icon-only mark (no wordmark) as the main logo on an AI-generated creative.** Without the wordmark next to it, the image model can hallucinate a distorted hybrid between the icon and the full lockup. Icon-only marks may stay in `reference/design-assets/` for other uses.

Secondary marks (e.g. a checkmark or seal version of the logo) are **not** the main logo. Reserve them for illustrating a specific claim or USP callout, never as the primary brand logo upload.

Accent/USP icon sets in `reference/design-assets/` that come in several color variants: pick the variant matching the color/flavor of the product featured in a given creative, not a fixed brand-wide accent.

---

## Ads Workflow

Say "let's make some new ads" (or similar) to trigger the `create-new-ads` skill, which runs four phases in one continuous context:

| Phase | Skill | What happens |
| --- | --- | --- |
| 1 | `competitor-analysis` | Analyses new images in `reference/ad-inspiration/`, logs them to the "Ad Inspiration" Sheet tab, translates patterns into 10+ concepts |
| 2 | `creative-strategy` | Picks 5–6 concepts, writes a 20-card Creative Brief (uses `static-ads-strategy` as its rubric) |
| 2.5 | *Human checkpoint* | User approves/edits/trims the brief |
| 3 | `creative-generation` | Generates approved creatives via Higgsfield (`gpt_image_2`) in SQ/VT/HZ sizes, uploads to Drive, logs to the "Creative Generations" tab |
| 3.5 | *Human checkpoint* | User reviews the actual images and requests regenerations |
| 4 | `meta-ads-campaign-setup` | Builds an ABO testing campaign in Meta Ads Manager. **Everything PAUSED. Nothing ever goes live.** |

Supporting skills: `static-ads-strategy` (the five-principle framework), `image-prompting/prompt-engineering.md` (prompt templates + rules), `skill-creator`, `mcp-integration`.

**Required connections:** Higgsfield connector, Meta Ads connector, the `gws` Google Workspace CLI, and a `META_ACCESS_TOKEN` in `.claude/settings.local.json`. See README.md.

---

## Commands

### /onboard

**Purpose:** One-time setup. Interviews the user, then fills in Brand Assets (above), `context/` files, `reference/meta-ads-config.json`, `reference/gws-config.json`, and the Meta token. Finishes with a readiness check. Safe to re-run to update any section.

### /prime

**Purpose:** Initialize a new session with full context awareness.

Run this at the start of every session. Claude will:

1. Read CLAUDE.md and context files
2. Summarize understanding of the user, workspace, and goals
3. Confirm readiness to assist (or point to `/onboard` if the workspace isn't configured)

### /create-plan [request]

**Purpose:** Create a detailed implementation plan before making changes.

Use when adding new functionality, commands, scripts, or making structural changes. Produces a thorough plan document in `plans/` that captures context, rationale, and step-by-step tasks.

Example: `/create-plan add a competitor analysis command`

### /implement [plan-path]

**Purpose:** Execute a plan created by /create-plan.

Reads the plan, executes each step in order, validates the work, and updates the plan status.

Example: `/implement plans/2026-01-28-competitor-analysis-command.md`

---

## Critical Instruction: Maintain This File

**Whenever Claude makes changes to the workspace, Claude MUST consider whether CLAUDE.md needs updating.**

After any change — adding commands, scripts, workflows, or modifying structure — ask:

1. Does this change add new functionality users need to know about?
2. Does it modify the workspace structure documented above?
3. Should a new command be listed?
4. Does context/ need new files to capture this?

If yes to any, update the relevant sections. This file must always reflect the current state of the workspace so future sessions have accurate context.

**Examples of changes requiring CLAUDE.md updates:**

- Adding a new slash command → add to Commands section
- Creating a new output type → document in Workspace Structure or create a section
- Adding a script → document its purpose and usage
- Changing workflow patterns → update relevant documentation

---

## Session Workflow

1. **First time only**: Run `/onboard`
2. **Start**: Run `/prime` to load context
3. **Work**: Use commands or direct Claude with tasks (e.g. "let's make some new ads")
4. **Plan changes**: Use `/create-plan` before significant additions
5. **Execute**: Use `/implement` to execute plans
6. **Maintain**: Claude updates CLAUDE.md and context/ as the workspace evolves

---

## Notes

- Keep context minimal but sufficient — avoid bloat
- Never commit secrets: tokens belong in `.claude/settings.local.json` (git-ignored), not `settings.json`
- Plans live in `plans/` with dated filenames for history
- Outputs are organized by type/purpose in `outputs/`
- Reference materials go in `reference/` for reuse
