# Ecommerce / Retail Sector Overlay

**Sector:** Ecommerce / Retail
**CONTENT_BRIEF.sector value:** `ecommerce-retail`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = ecommerce-retail`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility expectations, tone, product description accuracy, and pricing and claim discipline for Ecommerce / Retail content.

---

## Sector Scope

This overlay applies when the content subject falls within the Ecommerce / Retail sector. Typical signals include:

- Ecommerce stores and online retail platforms
- Direct-to-consumer brands and product lines
- Online product catalogues and listings
- Retail products and merchandise
- Fashion, lifestyle, and apparel products
- Consumer electronics and accessories
- Beauty, personal care, and wellness products
- Home goods and lifestyle merchandise

Common terminology signals: product, store, shop, retail, ecommerce, SKU, catalogue, inventory, checkout, online store, product listing, shopping cart.

**YMYL classification:** Non-YMYL for general product and retail content. Content involving health claims, safety implications, or significant financial decisions may carry elevated responsibility expectations. Standard generation rules apply in most cases.

---

## Product-Centric Content

Content in this sector frequently centres on products and purchasing decisions. The primary job of ecommerce and retail content is to help readers understand what a product is, what it does, and whether it suits their needs.

Generation skills may reference the following expectations when approaching product-focused content in this sector:

- Product descriptions should explain what the product is and what it does — not merely assert that it is good
- Features should be explained in practical, functional terms rather than listed without context
- Product comparisons should be clear, factual, and structured around meaningful criteria
- Content should support purchase decisions through useful information, not pressure tactics

This orientation applies across all formats. A product page, a review blog post, and a social post all benefit from prioritising practical understanding of the product over promotional enthusiasm.

---

## Terminology Guidance

Retail and ecommerce content uses product-focused terminology that should be applied accurately. Imprecise or exaggerated use of product terminology can erode trust with informed buyers.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Explain product features clearly and in accessible language
- Avoid unexplained retail, technical, or category-specific jargon
- Emphasise practical use cases rather than specification-heavy descriptions
- Assume the reader may be unfamiliar with industry shorthand (SKU, GSM, SPF, IP ratings, etc.) — explain before using

**Intermediate**
- Assume basic familiarity with the product category
- Reduce introductory explanations for well-understood product types
- Emphasise comparative understanding — how this product relates to alternatives or previous versions
- Use standard product terminology accurately without over-explaining

**Advanced**
- Precise product specification terminology may be used without simplification
- Assume familiarity with industry vocabulary, measurement standards, and technical classifications
- Prioritise accuracy and specificity in specification descriptions

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Pricing and Claim Discipline

Ecommerce content frequently involves pricing references and value claims. Generation skills may reference the following guidance when handling pricing and promotional language in this sector:

- Pricing references should be accurate when provided in the brief — do not round, approximate, or adjust figures supplied by the user
- Avoid implying specific prices if they are not confirmed in the brief or context material
- Avoid exaggerated product claims: "best ever", "perfect for everyone", "guaranteed results", and similar superlatives are not credible and may mislead buyers
- Product value should be communicated through features, benefits, and practical usage context rather than unsupported marketing assertions

When `CONTENT_BRIEF.content_points` references pricing, offers, or value propositions, generation skills can follow this discipline when working with those points.

---

## Credibility Expectations

Retail audiences expect product information that is clear, accurate, and trustworthy. Credibility in this sector is built through concrete product detail and honest representation of what a product does. Generation skills may reference the following guidance:

- Describe product features accurately — do not imply capabilities that are not confirmed in the brief or context material
- Product comparisons should remain factual and criterion-based rather than promotional
- Avoid fabricating customer outcomes or implying testimonial-style results that have not been supplied
- Avoid unverified product specifications — do not assert technical details (dimensions, materials, performance ratings) that the brief does not confirm

The standard to hold is: would an informed buyer find this accurate and useful, or would they find it misleading, vague, or overstated?

---

## Tone Calibration

The default tone for Ecommerce / Retail content balances clarity, helpfulness, and purchase-decision support. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Clear, practical explanations of product value and usage
- Confident product presentation grounded in specific features and benefits
- Helpful framing that supports the reader's purchasing decision
- Descriptive language that is specific rather than generic

**Avoid:**
- Overly aggressive promotional language that prioritises persuasion over accuracy
- Exaggerated urgency messaging ("buy now before it's gone forever", "limited time, act immediately")
- Unrealistic product promises that set expectations the product cannot meet
- Generic enthusiasm that could apply to any product in any category

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, aspirational lifestyle tone for a fashion or luxury brand), generation skills can follow the brief's tone while maintaining accuracy and avoiding unverified claims within that register.

---

## Product Description Guidance

Product descriptions in this sector benefit from explanations that connect features to real-world use. Generation skills may reference the following pattern when constructing product-focused content:

1. **What the product does** — describe the product's core function or purpose in concrete terms
2. **Why the feature matters** — explain the benefit or problem the feature addresses for the user
3. **How it is used in practice** — illustrate the product in a realistic usage context or scenario

**Avoid descriptions that:**
- List features without explaining what they mean for the user
- Rely entirely on promotional adjectives without substantive detail ("luxurious", "premium", "revolutionary" as standalone claims)
- Assume the reader already understands product specifications without explanation
- Imply product performance through assertion rather than explanation

When `CONTENT_BRIEF.content_points` references specific product features, variants, or use cases, generation skills can follow this explanatory pattern for each point.

---

## Disclaimer Awareness

Some ecommerce content requires disclaimers, particularly when content touches on product performance claims, warranties, return policies, or promotional offers. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- Makes specific product performance or results claims
- References warranties, guarantees, or return and refund policies
- Describes promotional pricing, discount offers, or limited-time terms
- Involves health, safety, or compliance implications for the product

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Ecommerce content carries hallucination risk in several specific areas. Generation skills may reference this guidance when applying source discipline in this sector:

- **Product specifications** — do not invent dimensions, materials, technical ratings, compatibility details, or performance specifications not confirmed in the brief or context material
- **Pricing** — do not fabricate prices, discounts, or comparative pricing figures
- **Product availability** — do not assert stock status, availability in specific regions, or delivery timelines unless confirmed in the source material
- **Warranties and return policies** — do not invent warranty durations, coverage terms, or return and refund conditions
- **Product performance claims** — do not fabricate efficacy results, test outcomes, or performance benchmarks
- **Customer outcomes** — do not invent testimonials, reviews, or user results that have not been supplied

All factual product claims should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe the product category and general use case rather than asserting specific product facts that have not been confirmed.
