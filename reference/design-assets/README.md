# Design Assets

Every non-product, on-brand visual asset the image model can use as a reference. The skills list this folder each batch and point to specific files **by exact filename**, so name files descriptively.

## Required

| File | What it is |
|---|---|
| `logo-full-width.png` | **Your primary logo lockup (icon + wordmark)** on a transparent background. This is the only logo uploaded on AI-generated creatives. If you use a different filename, set it in `CLAUDE.md` → Brand Assets. |

> ⚠️ Never use a **bare icon/symbol mark** (no wordmark) as the main logo reference for AI generation. Without the wordmark, the image model can hallucinate a distorted hybrid. Keep icon-only marks here for other uses if you like, but the skills won't upload them as the main logo.

## Optional (add whatever your brand has)

| Kind | Naming example | How the skills use it |
|---|---|---|
| Secondary marks (checkmark, seal) | `logo-checkmark.png` | Only to illustrate a specific claim/USP callout, never as the primary logo |
| Background textures | `<flavor-or-color>-background.jpg` | Matched to the featured product's color/flavor |
| USP / benefit icon sets | `<benefit>-icon-<color>.png` (e.g. `energy-icon-blue.png`) | Always rendered as a **row of 3–4 icons, each with a short label**, all in one color variant. Never a lone icon. Pick the color variant that matches the featured product. |
| Certification / trust icons | `<claim>-icon.png` (e.g. `third-party-tested-icon.png`) | Paired with the matching proof claim |
| Press logos ("As featured in") | `<publication>-logo-<color>.png` | Press-mention social proof. **Only include publications that have genuinely featured your brand.** |

Every claim an asset implies (a certification, a press mention) must also be listed in `context/offer-brief.md` → *Social proof assets*. Otherwise the skills won't use it.
