# Prompt Engineering Reference

> Load this on-demand when constructing complex prompts or when the user
> asks about prompt techniques. Apply these principles regardless of which
> image generation model is being used.

## The 5-Component Prompt Formula

> Write as natural narrative paragraphs — NEVER as comma-separated keyword lists.

### Component 1 — SUBJECT
Who or what is the primary focus. Be specific about physical characteristics,
material, species, age, expression. Never write just "a person" or "a product."

**Good:** "A weathered Japanese ceramicist in his 70s, deep sun-etched
wrinkles mapping decades of kiln work, calloused hands cradling a
freshly thrown tea bowl with an irregular, organic rim"

**Bad:** "old man, ceramic, bowl"

### Component 2 — ACTION
What the subject is doing, or the primary visual state. Use strong present-
tense verbs. "floats weightlessly," "holds a glowing lantern," "sits perfectly
still." If no action, describe pose or arrangement.

**Good:** "leaning forward with intense concentration, gently smoothing
the rim with a wet thumb, a thin trail of slip running down his wrist"

**Bad:** "making pottery"

### Component 3 — LOCATION / CONTEXT
Where the scene takes place. Include environmental details, time of day,
atmospheric conditions. "inside the cupola module of the International Space
Station," "on a rain-slicked Tokyo alley at 2am."

**Good:** "inside a traditional wood-fired anagama kiln workshop,
stacked shelves of drying pots visible in the soft background, late
afternoon light filtering through rice paper screens"

**Bad:** "workshop, afternoon"

### Component 4 — COMPOSITION
Camera perspective, framing, and spatial relationship. "medium shot centered
against the window," "extreme low-angle looking up," "bird's-eye view from
30 meters," "tight close-up on hands."

**Good:** "intimate close-up shot from slightly below eye level,
shallow depth of field isolating the hands and bowl against the
soft bokeh of the workshop behind"

**Bad:** "close up"

### Component 5 — STYLE (includes lighting)
The visual register, aesthetic, medium, and lighting combined. Reference real
cameras, film stock, photographers, publications, or art movements. Lighting
lives here as a sub-element, not a separate component.

**Good:** "shot on a Fujifilm X-T4 with warm color science and natural
bokeh, warm directional light from a single high window camera-left
creating gentle Rembrandt lighting on the face with deep warm shadows.
Reminiscent of Dorothea Lange's documentary portraiture"

**Bad:** "photorealistic, 8K, masterpiece" (see Banned Keywords below)

---

## Domain Mode Modifier Libraries

### Cinema Mode
**Camera specs:** RED V-Raptor, ARRI Alexa 65, Sony Venice 2, Blackmagic URSA
**Lenses:** Cooke S7/i, Zeiss Supreme Prime, Atlas Orion anamorphic
**Film stocks:** Kodak Vision3 500T (tungsten), Kodak Vision3 250D (daylight), Fuji Eterna Vivid
**Lighting setups:** three-point, chiaroscuro, Rembrandt, split, butterfly, rim/backlight
**Shot types:** establishing wide, medium close-up, extreme close-up, Dutch angle, overhead crane, Steadicam tracking
**Color grading:** teal and orange, desaturated cold, warm vintage, high-contrast noir

### Product Mode
**Surfaces:** polished marble, brushed concrete, raw linen, acrylic riser, gradient sweep
**Lighting:** softbox diffused, hard key with fill card, rim separation, tent lighting, light painting
**Angles:** 45-degree hero, flat lay, three-quarter, straight-on, worm's-eye
**Style refs:** Apple product photography, Aesop minimal, Bang & Olufsen clean, luxury cosmetics

### Portrait Mode
**Focal lengths:** 85mm (classic), 105mm (compression), 135mm (telephoto), 50mm (environmental)
**Apertures:** f/1.4 (dreamy bokeh), f/2.8 (subject-sharp), f/5.6 (environmental context)
**Pose language:** candid mid-gesture, direct-to-camera confrontational, profile silhouette, over-shoulder glance
**Skin/texture:** freckles visible, pores at macro distance, catch light in eyes, subsurface scattering

### Editorial/Fashion Mode
**Publication refs:** Vogue Italia, Harper's Bazaar, GQ, National Geographic, Kinfolk
**Styling notes:** layered textures, statement accessories, monochromatic palette, contrast patterns
**Locations:** marble staircase, rooftop at golden hour, industrial loft, desert dunes, neon-lit alley
**Poses:** power stance, relaxed editorial lean, movement blur, fabric in wind

### UI/Web Mode
**Styles:** flat vector, isometric 3D, line art, glassmorphism, neumorphism, material design
**Colors:** specify exact hex or descriptive palette (e.g., "cool blues #2563EB to #1E40AF")
**Sizing:** design at 2x for retina, specify exact pixel dimensions needed
**Backgrounds:** transparent (request solid white then post-process), gradient, solid color

### Logo Mode
**Construction:** geometric primitives, golden ratio, grid-based, negative space
**Typography:** bold sans-serif, elegant serif, custom lettermark, monogram
**Colors:** max 2-3 colors, works in monochrome, high contrast
**Output:** request on solid white background, post-process to transparent

### Landscape Mode
**Depth layers:** foreground interest, midground subject, background atmosphere
**Atmospherics:** fog, mist, haze, volumetric light rays, dust particles
**Time of day:** blue hour (pre-dawn), golden hour, magic hour (post-sunset), midnight blue
**Weather:** dramatic storm clouds, clearing after rain, snow-covered, sun-dappled

### Infographic Mode
**Layout:** modular sections, clear visual hierarchy, bento grid, flow top-to-bottom
**Text:** use quotes for exact text, descriptive font style, specify size hierarchy
**Data viz:** bar charts, pie charts, flow diagrams, timelines, comparison tables
**Colors:** high-contrast, accessible palette, consistent brand colors

### Abstract Mode
**Geometry:** fractals, voronoi tessellation, spirals, fibonacci, organic flow, crystalline
**Textures:** marble veining, fluid dynamics, smoke wisps, ink diffusion, watercolor bleed
**Color palettes:** analogous harmony, complementary clash, monochromatic gradient, neon-on-black
**Styles:** generative art, data visualization art, glitch, procedural, macro photography of materials

---

## Advanced Techniques

### Character Consistency (Multi-turn)
Maintain descriptive anchors across generations:
- First generation: establish the character with an exhaustive physical description
- Subsequent generations: reference "the same character" + repeat 2–3 key identifiers
- Key identifiers: hair color/style, distinctive clothing, facial feature

**Multi-image reference technique:**
- Provide multiple character reference images when the model supports it
- Assign distinct names to each character ("Character A: the red-haired knight")
- Model preserves features across different angles, actions, and environments
- Works best when reference images show the character from multiple angles

### Style Transfer Without Reference Images
Describe the target style exhaustively instead of referencing an image:
```
Render this scene in the style of a 1950s travel poster: flat areas of
color in a limited palette of teal, coral, and cream. Bold geometric
shapes with visible paper texture. Hand-lettered title text with a
mid-century modern typeface feel.
```

### Text Rendering Tips
- Quote exact text: `with the text "OPEN DAILY" in bold condensed sans-serif`
- **25 characters or less** — this is the practical limit for reliable rendering across most models
- **2–3 distinct phrases max** — more text fragments degrade quality
- Describe font characteristics, not font names
- Specify placement: "centered at the top third", "along the bottom edge"
- High contrast: light text on dark, or vice versa
- **Text-first approach:** Establish the text concept in your prompt early — models anchor on text mentioned in the opening of a prompt
- Expect creative font interpretations, not exact replication of described styles

### Positive Framing (Avoid Negative Instructions)
Most AI image models respond better to descriptions of what you want rather than what you don't want. Rephrase exclusions positively:
- Instead of "no blur" → "sharp, in-focus, tack-sharp detail"
- Instead of "no people" → "empty, deserted, uninhabited"
- Instead of "no text" → "clean, uncluttered, text-free"
- Instead of "not dark" → "brightly lit, high-key lighting"

For critical constraints, ALL CAPS emphasis can improve adherence:
- "MUST contain exactly three figures"
- "NEVER include any visible horizon line"
- "ONLY show the product, nothing else in frame"

---

## ❌ BANNED PROMPT KEYWORDS — NEVER USE THESE

These Stable Diffusion-era terms actively degrade output quality across modern AI image models. Avoid them entirely.

NEVER include:
- "4k" / "8k" / "ultra HD" / "high resolution" (use the model's resolution parameter instead)
- "masterpiece"
- "highly detailed" / "ultra detailed"
- "trending on artstation"
- "hyperrealistic" / "ultra realistic"
- "photorealistic" (describe the camera/film instead)
- "best quality"
- "award winning" (use specific publication names instead)

USE THESE INSTEAD (prestigious context anchors that actively improve composition):
- "Pulitzer Prize-winning cover photograph"
- "Vanity Fair editorial portrait"
- "National Geographic cover story"
- "WIRED magazine feature spread"
- "Architectural Digest interior"
- "Magnum Photos documentary"

---

## ⚠️ NEGATIVE PROMPTS

Most modern AI image models do not support a dedicated negative prompt parameter via API. Do not rely on negative instructions — use semantic reframing instead.

❌ WRONG: "no cars, no people, no clutter in the background"
✅ RIGHT: "an empty, deserted street, completely still, no signs of activity"

❌ WRONG: "no watermarks, no text"
✅ RIGHT: (add to prompt) "NEVER include any text, labels, or watermarks"

---

## Prompt Length Guide

| Use case | Target length | Notes |
|---|---|---|
| Quick draft / concept | 20–60 words (1–2 sentences) | Good for ideation |
| Standard generation | 100–200 words (3–5 sentences) | Production default |
| Complex professional | 200–300 words | Full 5-component treatment |
| Maximum specification | 300–500 words | Beyond this, returns diminish |

Quality and specificity matter more than hitting a word count. Do not pad prompts — every word should earn its place.

---

## Text Rendering in Images

Modern AI image models have improving but variable text rendering. Rules:
1. Enclose desired text in quotation marks in the prompt: "LAUNCH DAY"
2. Specify font characteristics explicitly: "bold white sans-serif," "elegant serif"
3. Specify placement: "centered at the bottom third," "upper left corner"
4. For complex layouts, describe text placement before requesting the image

Known limitation: Small text and complex multilingual text may require iterative refinement regardless of model.

---

## Prompt Adaptation Rules

When adapting prompts from other tools (Midjourney/DALL-E/etc.) to a natural language format:

| Source Syntax | Universal Equivalent |
|---|---|
| `--ar 16:9` | Set aspect ratio via the model's parameter, not in the prompt text |
| `--v 6`, `--style raw` | Remove — most APIs have no version/style flags |
| `--chaos 50` | Describe variety: "unexpected, surreal composition" |
| `--no trees` | Positive framing: "open clearing with no vegetation" |
| `(word:1.5)` weight | Descriptive emphasis: "prominently featuring [word]" |
| `8K, masterpiece, ultra-detailed` | Remove ALL of these — they are banned. Use prestigious context anchors instead |
| Comma-separated tags | Expand into descriptive narrative paragraphs |
| `shot on Hasselblad` | Keep — camera specs work well across all models |

---

## Common Prompt Mistakes

1. **Keyword stuffing** — stacking generic quality terms ("8K, masterpiece, best quality, ultra-realistic") actively degrades output. Use prestigious context anchors instead
2. **Tag lists** — AI image models want prose, not "red car, sunset, mountain, cinematic"
3. **Missing lighting** — The single biggest quality differentiator
4. **No composition direction** — Results in generic centered framing
5. **Vague style** — "make it look cool" vs specific art direction
6. **Ignoring aspect ratio** — Always set before generating
7. **Overlong prompts** — Diminishing returns past ~300 words; be precise, not verbose
8. **Text longer than ~25 characters** — Rendering degrades rapidly past this limit
9. **Burying key details at the end** — In long prompts, details placed last may be deprioritized; put critical specifics (exact text, key constraints) in the first third of the prompt
10. **Not iterating** — Use follow-up generations for progressive refinement instead of trying to get everything right in one attempt

---

## Proven Prompt Templates

> These patterns consistently produce high-quality results. Use as starting points and adapt to the request.

### The Winning Formula (Weight Distribution)

| Component | Weight | What to include |
|---|---|---|
| **Subject** | 30% | Age, skin tone, hair color/style, eye color, body type, expression |
| **Action** | 10% | Movement, pose, gesture, interaction, state of being |
| **Context** | 15% | Location + time of day + weather + context details |
| **Composition** | 10% | Shot type, camera angle, framing, focal length, f-stop |
| **Lighting** | 10% | Quality, direction, color temperature, shadows |
| **Style** | 25% | Art medium, brand names, textures, camera model, color grading |

### Instagram Ad / Social Media

**Pattern:** `[Subject with age/appearance] + [outfit with brand/texture] + [action verb] + [setting] + [camera spec] + [lighting] + [platform aesthetic]`

**Example (Product Placement):**
```
Hyper-realistic gym selfie of athletic 24yo influencer with glowing olive
skin, wearing crinkle-textured athleisure set in mauve. iPhone 16 Pro Max
front-facing portrait mode capturing sweat droplets on collarbones, hazel
eyes enhanced by gym LED lighting. Mirror reflection shows perfect form,
golden morning light through floor-to-ceiling windows. Frayed chestnut
ponytail with baby hairs, visible skin texture with natural erythema from
workout. Vanity Fair wellness editorial aesthetic.
```

**Example (Lifestyle Ad):**
```
A 24-year-old blonde fitness model in a high-energy sports drink
advertisement. Mid-run on a beach, wearing a vibrant orange sports bra
and black shorts, playful smile and sparkling blue eyes exuding vitality.
Bottle of the drink held in hand, waves crashing in background. Shot on
Nikon D850 with 70-200mm f/2.8 lens, natural light, fast shutter speed
capturing motion. Visible skin texture, water droplets, product label
clearly visible. National Geographic fitness feature aesthetic.
```

**Example (Luxury Lifestyle):**
```
Gorgeous Instagram model wearing a designer silk gown, luxury rooftop
restaurant, golden hour lighting, champagne in hand, luxurious aspirational
lifestyle. Captured with Sony A7R IV, 85mm f/1.4 lens, shallow depth of
field, warm color grading.
```

### Product / Commercial Photography

**Pattern:** `[Product with brand/detail] + [dynamic elements] + [surface/setting] + "commercial photography for advertising campaign" + [lighting] + [prestigious publication reference]`

**Example (Beverage):**
```
Gatorade bottle with condensation dripping down the sides, surrounded by
lightning bolts and a burst of vibrant blue and orange light rays. The
Gatorade logo is prominently displayed on the bottle, with splashes of
water frozen in mid-air. Commercial food photography for an advertising
campaign, vibrant complementary colors. Bon Appetit magazine cover aesthetic.
```

**Example (Food):**
```
In and Out burger with layers of fresh lettuce, melted cheese, and pretzel
bun, placed on a white surface with the In and Out logo subtly glowing in
the background. Falling french fries and golden light, warm scene.
Commercial food photography for an advertising campaign, vibrant
complementary colors. Shot in the style of a Bon Appetit feature spread.
```

### Fashion / Editorial

**Pattern:** `[Subject with ethnicity/age/features] + [outfit with texture/brand/cut] + [location] + [pose/action] + [camera + lens] + [lighting quality]`

**Example (Street Style):**
```
A 24-year-old female AI influencer posing confidently in an urban cityscape
during golden hour. Flawless sun-kissed skin, long wavy brown hair, deep
green eyes. Wearing a chic streetwear outfit — oversized beige blazer,
white top, high-waisted jeans. Captured with Sony A7R IV at 85mm f/1.4,
shallow depth of field with warm golden bokeh.
```

**Example (High Fashion):**
```
Stunning 24-year-old woman, long platinum blonde hair, radiant skin,
piercing blue eyes, dressed in a chic pastel blazer with a modern
minimalist aesthetic, soft sunlight glow, high-end fashion appeal.
Shot on Canon EOS R5, 85mm f/1.2 lens.
```

### SaaS / Tech Marketing

**Pattern:** `[UI mockup or abstract visual] + "on [dark/light] background" + [specific colors with hex] + [typography description] + "clean, premium SaaS aesthetic" + [glassmorphism/gradient/glow effects]`

**Example (Dashboard Hero):**
```
A floating glassmorphism UI card on a deep charcoal background showing a
content analytics dashboard with a rising line graph in teal (#14B8A6),
bar charts in coral (#F97316), and a circular progress indicator at 94%.
Subtle grid lines, frosted glass effect with 20% opacity, teal glow
bleeding from the card edges. Clean premium SaaS aesthetic, no text
smaller than headline size.
```

### Logo / Branding

**Pattern:** `[Product/bottle/item] + "with [brand element] prominently displayed" + [dynamic visual elements] + "commercial photography" + [lighting style] + [prestigious publication reference]`

**Example:**
```
A sleek matte black bottle with a minimal white logo mark centered on the
label, surrounded by swirling gradient ribbons of teal and coral light.
The bottle sits on a reflective dark surface, sharp studio rim lighting
separating it from the background. Product photography for luxury
branding, dramatic contrast. Wallpaper* magazine design editorial.
```

---

### Branded Paid Social Ad (Lifestyle/Product)

Write this as a literal, spatially-organized **spec** using markdown headings and
bullets — not flowing narrative prose. Every design element gets an explicit position.

Placeholders below come from `CLAUDE.md`'s **Brand Assets** section:
`[Brand]` (brand name), `[primary color hex]`, `[headline font]` +
`[headline font description]`, `[body font]`, and the primary logo file.

**Pattern:**
```
Create a [scene mood] promotional image for a Facebook/Instagram paid ad for [Brand].

## Design Layout:
- **Background:** [continuous photographic scene description] — no separate colored panels, bands, or sections at any point
- **Top of image (only if this card's brief includes a logo):** [Brand] logo (attached) centred, with clear open space around it
- **[Badge position, only if this creative carries one]:** [badge described as rendered text per the Visual Variety menu, OR as an uploaded graphic asset annotated "(attached)"]
- **Main visual:** The [product type] (as shown in attached image), [pose/placement]. [If a grounded look is wanted and the Product photography quirks call for an override, add it here, e.g. "Remove the floating pieces next to the pack."]
- **[Background detail/props, if applicable]:** [supporting prop description]
- **[Middle strip, centered horizontally, above the headline — only if this creative carries a USP Icon Row]:** [3–4 icon+text USP callouts in a horizontal row, no background box behind any of them, per the USP Icon Row rule below]
- **Lower third of image:** Headline text "[exact headline]" in [headline font] font ([headline font description]) with clear space around it, overlaid directly on the photographic background — never on a separate panel

## Visual Style:
- [Scene mood restatement]
- [Lighting description]
- Shot on a [camera], [lens], [depth-of-field/colour notes]
- [Prestigious editorial aesthetic anchor]
- Clean, uncluttered, high-end DTC feel
```

Every element appears once. The headline is always quoted exactly. The social proof
badge (when the creative has one) is named at a single position — never restated
elsewhere in the same prompt. The logo bullet appears only when this card's brief
specifies a logo (~3 in 4 cards — see `creative-strategy`'s Logo Frequency rule);
omit it entirely for the remaining ~1 in 4.

**Product identity rule — point at the reference image, don't re-describe it.** Name
only the generic product-type noun matching what the brand sells (supplement pouch,
skincare bottle, coffee bag, sneaker, light fixture, etc. — read from context, never
invented) so the sentence is grammatical, then instruct the model to use the attached
images for everything else: `"Main visual: the [product type] shown in the images
attached…"`. Never write out the product's shape, material, finish, or label design in
prose — asking the model to reconstruct the look from a text description is what
produces a rendered product that doesn't match the real one. The uploaded reference
images are the single source of truth for appearance; let the model copy them.

**Floating vs. grounded props — scene-mode dependent, correctable via an explicit
removal instruction.** Whether loose product pieces (capsules, gummies, beans,
accessories, etc.) may float or tumble in mid-air depends on the scene mode the brief
card's `Visual direction` names:

- **Stylized / product-focus-only scenes** (clean studio backdrop, gradient, no
  realistic human/setting context) — floating, tumbling, mid-air pieces remain a
  valid creative choice and can read as dynamic/energetic.
- **Lifestyle / realistic scenes** (kitchen counter, gym, desk, hand-holding, or any
  setting meant to read as a believable real-world moment) — every prop must be
  grounded: resting on a surface, held in a hand, sitting in a bowl/dish, etc. Never
  describe pieces as floating, tumbling, or suspended in a realistic scene — it reads
  as physically implausible and breaks the scene's believability.

**Reference photos with baked-in elements.** If `reference/products/README.md` →
*Product photography quirks* notes that the reference photos include something a
card doesn't want (e.g. floating pieces beside the pack), the Main visual bullet must
explicitly override it ("Remove the floating pieces next to the pack") whenever a
grounded look is wanted. When a stylized card wants to keep that look, omit the
override and let the reference photo stand.

**Typography rule — two-font system, always describe the headline font.** The brand
uses two fonts, each with a fixed job — see `CLAUDE.md`'s Brand Assets. Custom or
licensed fonts are fonts the image model has never seen; with the name alone it
guesses, often rendering the wrong weight (e.g. an unwanted heavy/bold serif). So:

- **[headline font]** — reserved exclusively for the main headline text. Always named
  AND described together, verbatim from Brand Assets: `"in [headline font] font
  ([headline font description])"`. Never use it for subline, badge, additional copy,
  or the logo.
- **[body font]** — every other rendered text element: subline, social-proof badge
  text, and any additional on-image copy (price anchor, callout). Named alone unless
  Brand Assets gives a description, e.g. `"in white [body font] text"`. (Primary/caption
  text is never image-rendered, so it needs no font instruction.)

Never let the headline font appear anywhere but the headline, and never let the body
font appear in the headline. Never introduce a third font, script, or decorative face.

**Social proof treatment — rotate visual styles, never one fixed pattern.** Do not
default every creative to the same rectangular badge in the brand colour sitting
directly below the headline — repeating one shape/colour/position across a batch reads
as template fatigue. For each creative that carries an on-image badge, pick one
treatment from this menu, and vary the choice across the batch (don't let every
creative in a batch land on the same one):

- **Rectangular / pill badge** — solid or outlined, in [primary color hex] or white
  with [primary color hex] text/outline — position it below the headline, beside it,
  or in a corner; vary the position too, not just the shape.
- **Icon + text lockup, no background box** — an icon (checkmark, shield, star) inline
  beside small bold text directly on the photo, with no solid background shape at all.
- **Corner seal / starburst** — a small circular or starburst badge-of-honour tucked
  into a frame corner, separate from the headline area entirely.
- **Inline checkmark treatment** — a single checkmark icon immediately before or after
  a short proof phrase, styled as part of the headline/subline area rather than a
  standalone badge.
- **Uploaded graphic asset** — an actual trust/certification badge file from
  `reference/design-assets/`, uploaded as a `medias` reference and referred to in the
  prompt as "(attached)" rather than described as rendered text.

Whichever treatment is chosen, name it explicitly and give it one named position —
never restate the social proof element elsewhere in the same prompt.

**Star icon rule for review/rating proof.** Whenever the social proof claim is review-
or rating-based (e.g. "[N]+ five-star reviews," "4.8/5 rating"), the prompt must call
for a rendered graphic star element — `★★★★★` as five rendered star glyphs, or a small
star icon beside the number — never rating text alone with no visual star.
Certification/count-based proof (a certification, "[N]+ customers", third-party tested)
doesn't need a star — use one of the other treatments above instead.

**Frequency.** Not every creative in a batch carries an on-image badge — see
`static-ads-strategy`'s Principle 3 for the current threshold (currently 1 in every 2
creatives). When a creative's brief card isn't one of the ones assigned an on-image
badge, omit the social-proof bullet from Design Layout entirely — the primary
text/caption still carries the same proof point; it's just not rendered as on-image
graphic copy for that creative.

---

**USP Icon Row — a separate design element from the social proof badge above.**
A creative may use neither, either, or both of the social proof badge and the USP
Icon Row — they serve different jobs (trust/credibility vs. a themed benefit
summary) and each follows its own independent rule; never conflate the two or
treat the icon row as a badge variant.

When a brief card calls for USP/benefit icons from `reference/design-assets/`, they
are **always rendered as a themed group of 3–4 icons in a single horizontal row,
centered horizontally, each icon immediately paired with a short grounded benefit
label, all icons in the same color variant, with no background box behind any of
them** — never as a single lone icon dropped into a corner with no label. This
mirrors the pattern used by top-performing DTC ads (e.g. Lemme Purr: "pH Balance" /
"Vaginal Microbiome" / "Yeast Balance" / "Vaginal Health"; Freja Bone Broth:
"Regular Bowels" / "Healthy Joints" / "Increased Hormonal Balance" / "Stronger Hair &
Nails" — both in `reference/ad-inspiration/`) — a horizontal row of 3–4 icons, each
with a short label directly beneath or beside it, centered as a group, no background
box.

The benefit label paired with each icon must be short (one or two words, or a
short phrase) and grounded in `context/offer-brief.md` / `context/business-info.md`
— never an invented or generic claim.

**Canonical proven prompt language** (confirmed correct in testing — full worked
prompt in Example 4 below):
```
Middle strip, centered horizontally, above the headline: Four icon+text USP
callouts in a horizontal row, no background box behind any of them: the
[icon-1] icon (attached) beside "[Benefit 1]" in white [body font] text;
the [icon-2] icon (attached) beside "[Benefit 2]"; the [icon-3] icon (attached)
beside "[Benefit 3]"; the [icon-4] icon (attached) beside "[Benefit 4]" — all
four icons in their [color] colour variant
```
Use this pattern as the model for any USP Icon Row bullet: name each icon file
attached, pair it with its label in quotes, state the text font (body font), and
state the single shared color variant once at the end rather than repeating it per
icon.

**Icons used this way must be uploaded as `medias` references**, annotated
"(attached)" in the prompt exactly like a badge graphic asset — never described
from scratch in prose (the model doesn't know what your "energy icon" looks like
without the reference image).

**Never use a USP/benefit icon as a single, unpaired, uncaptioned corner accent.**
If a brief card's `Visual direction` only calls for "a small icon accent" with no
group and no label, that instruction is invalid — flag it back to creative-strategy
or, if generating directly, expand it into a full 3–4 icon row with labels before
prompting.

---

**Anti-sectioning rule — overlay text on the continuous photo, never a separate
panel.** The headline (and any badge/subline) must sit directly on top of the same
continuous photographic background as the rest of the creative — describe it as
overlaid/superimposed on the photo. Never describe or imply a separate solid-colour
panel, band, or section (e.g. a distinct off-white strip across the bottom third) that
visually splits the frame into a "photo part" and a "text part." Framing language like
"in the lower third of the image" means positioned within that region of the same
continuous photograph — not a distinct colour block.

> **This is not a conditional/"add it if it seems ambiguous" rule — it is mandatory,
> every time.** In testing, a prompt that used only the plain phrase *"...appears in
> large bold black sans-serif font in the lower third of the image"* — with no other
> panel/box language anywhere — still rendered a distinct solid-colour band splitting
> the photo from the text. That phrase alone was enough to trigger it. So the
> Background bullet's "no separate colored panels, bands, or sections" clause and the
> headline bullet's "never on a separate panel" clause must both appear every time —
> not just when a prompt "seems risky."

**Four worked examples** (Square format; adapted from real, tested prompts — brand
values shown as placeholders. The first three include the logo and a grounded product
shot, but both remain per-card decisions; the fourth demonstrates the USP Icon Row
pattern):

**Example 1 — Kitchen lifestyle, no badge, grounded:**
```
Create a realistic kitchen lifestyle promotional image for a Facebook/Instagram paid ad for [Brand].

## Design Layout:
- **Background:** Continuous photographic scene of a light wood kitchen countertop — no separate colored panels, bands, or sections at any point
- **Top of image:** [Brand] logo (attached) centred, with clear open space around it
- **Main visual:** The [product type] (as shown in attached image), standing upright and in sharp, tack-sharp focus on the light wood countertop.
- **Background detail:** A generic, unbranded version of the inconvenient alternative (e.g. a gritty powder shaker bottle) visible out of focus behind the product, illustrating the contrast — no competitor branding visible
- **Lower third of image:** Headline text "[Exact headline]" in white [headline font] font ([headline font description]), overlaid directly on the photographic background — never on a separate panel

## Visual Style:
- Realistic kitchen lifestyle scene
- Soft natural window light from camera-left
- Shot on a Sony A7R IV, 50mm f/2.8 lens, shallow depth of field keeping only the product tack-sharp
- Real Simple lifestyle-wellness editorial aesthetic
- Clean, uncluttered, high-end DTC feel
```

**Example 2 — Lifestyle with model, text-based star badge, grounded:**
```
Create a realistic bright gym lifestyle promotional image for a Facebook/Instagram paid ad for [Brand].

## Design Layout:
- **Background:** Continuous photographic scene of a bright gym with sunlight streaming through large windows
- **Top of image:** [Brand] logo (attached) centred, with clear open space around it
- **Top-right corner:** Circular seal badge in [primary color hex] reading "★★★★★ [N]+ Reviews" in white [body font] text, with the five stars rendered as solid gold star glyphs beside the number
- **Main visual:** The [product type] (as shown in attached images), held by [avatar description, e.g. a toned woman in her 30s] whose hand and forearm are visible.
- **Lower third of image:** Headline text "[Exact headline]" in [headline font] font ([headline font description]) with clear space around it, overlaid directly on the photographic background — never on a separate panel

## Visual Style:
- Realistic bright gym lifestyle scene
- Sunlight streaming through large windows
- Shot on a Sony A7R IV, 85mm f/1.8 lens, shallow depth of field
- Warm, energetic color grading
- Women's Health fitness editorial aesthetic
- Clean, uncluttered, high-end DTC feel
```

**Example 3 — Product editorial, attached-graphic badge, grounded:**
```
Create a bold product editorial promotional image for a Facebook/Instagram paid ad for [Brand].

## Design Layout:
- **Background:** Bold, high-contrast dark-to-[packaging colour] gradient complementing the packaging of the product.
- **Top of Image:** [Brand] logo (attached) centred in the top of the image with clear open space around it.
- **Main visual:** The [product type] (as shown in attached images), standing upright on a dark stone-style surface with a small scattering of [key ingredient/material] around its base — viewed at a slight three-quarter angle, product prominently displayed.
- **Lower third of image:** Headline text "[Exact headline]" in white [headline font] font ([headline font description]), overlaid directly on the background.
- **Below headline:** The horizontal [certification] badge (attached) with [primary color hex] accent colouring and clear open space around it.

## Visual Style:
- Bold product editorial scene
- Hard directional key light with a fill card creating crisp rim separation on the product
- Shot on a Sony A7R IV, 50mm f/2.8 lens, punchy colour science, shallow depth of field
- GQ product editorial aesthetic
- Clean, uncluttered, high-end DTC feel
```

**Example 4 — USP Icon Row, problem/solution split, no logo:**
```
Create a realistic lifestyle problem/solution promotional image for a Facebook/Instagram paid ad for [Brand].

## Design Layout:
- **Background:** Continuous photographic scene of a [setting, e.g. gym bench], split naturally left-to-right within one seamless frame — no separate colored panels, bands, or sections at any point
- **Left side:** A generic, unbranded [old/inconvenient alternative] under slightly dim, unflattering light — no competitor branding visible
- **Right side:** The [product type] (as shown in the images attached), standing upright on the same surface in bright, appetizing light.
- **Middle strip, centered horizontally, above the headline:** Four icon+text USP callouts in a horizontal row, no background box behind any of them: the [icon-1] icon (attached) beside "[Benefit 1]" in white [body font] text; the [icon-2] icon (attached) beside "[Benefit 2]"; the [icon-3] icon (attached) beside "[Benefit 3]"; the [icon-4] icon (attached) beside "[Benefit 4]" — all four icons in their [color] colour variant
- **Lower third of image, centered:** Headline text "[Exact headline]" in [headline font] font ([headline font description]), centered horizontally in the frame, overlaid directly on the photographic background — never on a separate panel
- **Below headline, centered:** Subline text "[Exact subline]" in white [body font] text, centered horizontally, overlaid directly on the photographic background

## Visual Style:
- Realistic lifestyle scene with a clear side-by-side lighting contrast — dim and flat on the old-alternative side, bright and warm on the product side
- Shot on a Sony A7R IV, 50mm f/2.8 lens, shallow depth of field
- Men's Health editorial aesthetic
- Clean, uncluttered, high-end DTC feel
```
This card's brief had `Logo: No`, so there's no logo bullet — the icon row and headline/subline centering are the elements to note here.

---

### Vertical Adaptation Prompt

Three media references are required for every adaptation call: the original square
image and the creative's original product reference image, always — plus the primary
logo file only when that card's brief includes the logo. Omitting the product image
(or the logo, when the card has one) risks the model hallucinating that element during
recomposition. The product's own size and proportions must never change during
recomposition — only the surrounding space and the logo/headline sizing adjust; if
there's any risk of the model stretching the product, restate "keep the [product]'s
size and proportions unchanged" in the Sizing bullet below.

**Pattern:**
```
Adapt this ad (attached) to vertical 9:16 format.

## Elements to Preserve:
- **Headline** "[exact headline]"
- **Logo (only if this card includes one)** Maintain the centred logo (attached) at the top of the image with clear space around it.
- **Background** [restate background/setting]
- **Product** [Product name] (attached) placed [restate placement]

## Elements to Change:
- **Aspect Ratio**: Change the aspect ratio to vertical 9:16 format.
- **More Spacing**: Give the design elements more breathing room to fill out the taller format.
- **Composition**: Adapt the composition of the photograph to suit the taller frame.
- **Sizing**: Increase the size of the logo and headline slightly to account for the increased vertical space.

## Rules:
- **Danger Zone**: In vertical Meta ad placements, the bottom 25% of the frame (roughly the bottom 450 pixels of a 1920px-tall image) is blocked by the caption, audio track, and engagement bar. Ensure none of the key design elements (logo, headline, product) are placed within this zone.
```

This is bespoke per creative, never a single fixed string reused across a batch —
restate the actual headline/background/product placement of the specific creative
being adapted, not a generic placeholder. Omit the Logo bullet entirely for a card
whose brief specifies no logo.

**Worked example** (adapting Example 1 above):
```
Adapt this ad (attached) to vertical 9:16 format.

## Elements to Preserve:
- **Headline** "[Exact headline from the square]"
- **Logo** Maintain the centred logo (attached) at the top of the image with clear space around it.
- **Background** Kitchen counter with natural lighting and an out-of-focus shaker bottle behind the product.
- **Product** [Brand] [product name] (attached) placed on the counter in the centre of the ad.

## Elements to Change:
- **Aspect Ratio**: Change the aspect ratio to vertical 9:16 format.
- **More Spacing**: Give the design elements more breathing room to fill out the taller format.
- **Composition**: Adapt the composition of the photograph to suit the taller frame.
- **Sizing**: Increase the size of the logo and headline slightly to account for the increased vertical space.

## Rules:
- **Danger Zone**: In vertical Meta ad placements, the bottom 25% (about the bottom 450 pixels) is blocked by the caption, audio track, and engagement bar. Ensure that none of the key design elements are placed within this zone.
```

---

### Horizontal Adaptation Prompt

Same three media references as the vertical adaptation. The product/main visual
always goes on the **right side** of the frame; the headline is always centred
vertically on the **left side**, adjacent to it.

**Pattern:**
```
Adapt this ad (attached) to horizontal 16:9 format.

## Elements to Preserve:
- **Headline** "[exact headline]" centred vertically on the side adjacent to the main visual of the ad.
- **Logo (only if this card includes one)** Maintain the centred logo (attached) at the top of the image with clear space around it.
- **Background** [restate background/setting]
- **Product** [Product name] (attached) placed on the counter on the right side of the ad.

## Elements to Change:
- **Aspect Ratio**: Change the aspect ratio to horizontal 16:9 format.
- **More Spacing**: Give the design elements more breathing room to fill out the wider format.
- **Composition**: Adapt the composition of the photograph to suit the wider frame.
- **Sizing**: Increase the size of the logo and headline slightly to account for the increased horizontal space.
```

Omit the Logo bullet entirely for a card whose brief specifies no logo.

**Worked example** (adapting Example 1 above):
```
Adapt this ad (attached) to horizontal 16:9 format.

## Elements to Preserve:
- **Headline** "[Exact headline from the square]" centred vertically on the side adjacent to the main visual of the ad.
- **Logo** Maintain the centred logo (attached) at the top of the image with clear space around it.
- **Background** Kitchen counter with natural lighting and an out-of-focus shaker bottle behind the product.
- **Product** [Brand] [product name] (attached) placed on the counter on the right side of the ad.

## Elements to Change:
- **Aspect Ratio**: Change the aspect ratio to horizontal 16:9 format.
- **More Spacing**: Give the design elements more breathing room to fill out the wider format.
- **Composition**: Adapt the composition of the photograph to suit the wider frame.
- **Sizing**: Increase the size of the logo and headline slightly to account for the increased horizontal space.
```

---

### Key Tactics That Make Prompts Work

1. **Name real cameras** — "Sony A7R IV", "Canon EOS R5", "iPhone 16 Pro Max" anchor realism
2. **Specify exact lens** — "85mm f/1.4" gives the model precise depth-of-field information
3. **Use age + ethnicity + features** — "24yo with olive skin, hazel eyes" beats "a person"
4. **Name brands for styling** — "Lululemon mat", "Tom Ford suit" triggers specific visual associations
5. **Include micro-details** — "sweat droplets on collarbones", "baby hairs stuck to neck"
6. **Add platform context** — "Instagram aesthetic", "commercial photography for advertising"
7. **Describe textures** — "crinkle-textured", "metallic silver", "frosted glass"
8. **Use action verbs** — "mid-run", "posing confidently", "captured mid-stride"
9. **Use prestigious context anchors** — "Pulitzer Prize-winning photograph," "Vanity Fair editorial," "National Geographic cover" actively improve quality. NEVER use "ultra-realistic," "8K," "masterpiece" — these are banned
10. **For products, say "prominently displayed"** — ensures the product/logo isn't hidden

### Anti-Patterns (What NOT to Do)

- **"A dark-themed Instagram ad showing..."** — too meta, describes the concept not the image
- **"A sleek SaaS dashboard visualization..."** — abstract, no visual anchors
- **"Modern, clean, professional..."** — vague adjectives that mean nothing to the model
- **"A bold call to action with..."** — describes marketing intent, not visual content
- **Describing what the viewer should feel** — instead, describe what creates that feeling

---

## Safety Filter Rephrase Strategies

AI image models apply content safety filters that cannot be disabled. When a generation is blocked, rephrase rather than retry the same prompt.

### Common Trigger Categories

| Category | Triggers on | Rephrase approach |
|---|---|---|
| Violence/weapons | Combat, blood, injuries, firearms | Use metaphor or aftermath: "battle-worn" → "weathered veteran" |
| Medical/gore | Surgery, wounds, anatomical detail | Abstract or clinical: "open wound" → "medical illustration" |
| Real public figures | Named celebrities, politicians | Use archetypes: "a tech entrepreneur in a minimalist office" |
| Children + risk | Minors in any ambiguous context | Add safety context: specify educational, family, or playful framing |
| NSFW/suggestive | Revealing clothing, intimate poses | Use artistic framing: "fashion editorial, fully clothed, editorial pose" |

### Rephrase Patterns

1. **Abstraction** — Replace specific problematic elements with abstract concepts
2. **Artistic framing** — Frame content as art, editorial, or documentary
3. **Metaphor** — Use symbolic language instead of literal descriptions
4. **Positive emphasis** — Describe what IS present, not what's problematic
5. **Context shift** — Move from threatening to educational/professional context

### Example Rephrases

| Blocked prompt | Successful rephrase |
|---|---|
| "a soldier in combat firing a rifle" | "a determined soldier standing guard at dawn, rifle slung over shoulder, morning mist over the outpost" |
| "a scary horror monster" | "a fantastical creature from a dark fairy tale, intricate organic textures, bioluminescent accents, concept art style" |
| "medical surgery scene" | "a clean modern operating room viewed from the observation gallery, soft blue surgical lights, professional documentary style" |
| "celebrity portrait of [name]" | "a distinguished middle-aged man in a tailored navy suit, warm studio lighting, editorial portrait style" |

### Key Principle

Safety filters analyse the generated image, not just the prompt. Even well-phrased prompts can be blocked if the model's interpretation triggers the filter. When this happens, shift the visual concept further from the trigger rather than just changing words.
