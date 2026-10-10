# Product Images

Put your product photography here, with **one folder per product** (or per flavor/variant/bundle). The folder name *is* the product identifier. The skills list this directory and use the exact folder names in the Creative Brief's `Product to feature` field. **Only products with a folder here can be advertised in a batch.**

```
reference/products/Images/
├── product-a/
│   ├── product-a-hero.png              # clean packaging/hero shot (transparent or plain background)
│   ├── product-a-lifestyle.png         # in-context / ingredient / lifestyle shot
│   └── product-a-graphic-benefits.png  # supporting marketing graphic (optional)
├── product-b/
│   └── ...
└── bundles/
    └── ...
```

## Filename conventions

There are no SKUs or spec files. Filenames describe what the image shows, and the generation skill picks references based on them:

| Pattern | Type | Used for |
|---|---|---|
| `*-hero.png`, `*-packshot.png` (or e.g. `*-pouch.png`, `*-bottle.png`, `*-box.png`) | Clean packaging/hero shot | Product-hero, trust-callout, direct-response creatives |
| `*-lifestyle.png`, `*-ingredient-background.png`, `*-in-use.png` | Lifestyle / ingredient-context shot | Lifestyle, transformation, social-proof creatives |
| `*-graphic-*.png` | Supporting marketing graphic | Trust/education-adjacent concepts, used sparingly |

High-resolution PNGs with clean edges give the best product fidelity. The image model copies the packaging from these references, so the label should be sharp and legible.

Product claims (dosage, ingredients, certifications, materials, sizes) **don't** go here. They belong in `context/offer-brief.md` and `context/business-info.md`, and the skills only ever use claims from those files.

## Product photography quirks

Record anything about your reference photos that the image model needs to work around. `creative-generation` reads this section before writing prompts. Delete the example below and add your own.

> **Example:** "Our standard pouch reference photos have loose product pieces floating beside the pack. For grounded/lifestyle scenes, the prompt must say 'Remove the floating pieces next to the pack' to override what the reference shows."

- _(none yet)_
