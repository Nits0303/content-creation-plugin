# Tech Sector Overlay

**Sector:** IT / SaaS / Tech
**CONTENT_BRIEF.sector value:** `it-saas`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = it-saas`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, tone, credibility, and technical depth for IT / SaaS / Tech content.

---

## Sector Scope

This overlay applies when the content subject falls within the IT / SaaS / Tech sector. Typical signals include:

- SaaS platforms and subscription software
- APIs and developer tools
- AI tools and automation platforms
- Cloud services and infrastructure
- Software dashboards and productivity tools
- Integrations, workflows, and data pipelines
- Engineering and product documentation

**YMYL classification:** Non-YMYL. Standard generation rules apply. No regulatory disclaimer requirements unless `CONTENT_BRIEF.disclaimers_required` or `CONTENT_BRIEF.sector_flags` indicate otherwise.

---

## Terminology Guidance

Tech content may include technical terminology, product architecture terms, API and developer language, and engineering vocabulary. This is expected and appropriate for the sector.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating technical density for this sector.

**Beginner**
- Define technical terms on first use
- Prefer plain-language explanations alongside or instead of jargon
- Use analogies to familiar concepts where helpful
- Do not assume familiarity with acronyms (API, SDK, CI/CD, etc.) — spell out and briefly explain

**Intermediate**
- Assume basic familiarity with common concepts and tools
- Reduce or omit basic definitions for standard terminology
- Use accurate terminology without extensive scaffolding
- Analogies remain useful for complex or abstract architectural concepts

**Advanced**
- Use precise technical vocabulary without simplification
- Assume familiarity with architecture patterns, tooling ecosystems, and developer workflows
- Avoid over-explaining established concepts — it reads as condescending to expert audiences
- Prioritise accuracy and specificity

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Credibility Expectations

Tech audiences hold high expectations for accuracy. Credibility in this sector is built through precision, not enthusiasm. Generation skills may reference the following guidance:

- Use specific terminology correctly — imprecise use of technical terms damages trust with informed readers
- Ground feature explanations in function — describe what something does and how, not just that it exists
- Use concrete examples and realistic usage scenarios in preference to abstract claims
- Avoid unsubstantiated superlatives — claims like "the most powerful" or "best-in-class" without supporting evidence read as marketing noise to tech audiences

The standard to hold is: would a technically informed reader find this accurate and specific, or would they find it vague and promotional?

---

## Tone Calibration

The default tone for tech sector content is clear, competent, and precise. Professionalism is expected. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Direct, functional language
- Confident assertions grounded in specifics
- Matter-of-fact explanations of how things work
- Appropriate use of technical voice when the audience warrants it

**Avoid:**
- Hype-driven marketing language ("revolutionary", "game-changing", "unprecedented")
- Vague buzzwords used without explanation ("AI-powered", "seamless", "robust" as standalone claims)
- Exaggerated benefit claims without grounding in actual functionality
- Excessive enthusiasm that substitutes for substance

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a tone that diverges from these defaults (e.g. conversational for a beginner audience), generation skills can follow the brief and reference these calibrations within that tonal register, not override it.

---

## Technical Depth Control

Generation skills may reference this guidance when adapting content depth based on `CONTENT_BRIEF.reader_knowledge_level`. This controls how much explanation, scaffolding, and contextualisation is appropriate.

**Beginner**
- Introduce concepts before using them
- Provide conceptual explanations before procedural ones
- Use step-by-step structure for any instructional content
- Avoid assumed context about tooling ecosystems or architecture patterns

**Intermediate**
- Move more quickly through foundational concepts
- Focus depth on the specific topic rather than surrounding context
- Use standard tooling and terminology without extensive preamble
- Provide examples that assume basic operational familiarity

**Advanced**
- Assume the reader can orient themselves
- Deliver depth directly — skip conceptual scaffolding unless the specific concept is genuinely novel
- Allow architectural and systems-level detail
- Prioritise accuracy and completeness over accessibility

Depth calibration applies across all formats (blog, webpage, social). Social content in this sector should compress technical depth appropriately for the platform while preserving accuracy.

---

## Product & Feature Explanation Guidance

Tech content frequently requires explaining products, features, integrations, and workflows. Generation skills may reference the following pattern when describing product functionality:

1. **What it does** — describe the feature or capability in functional terms
2. **Why it matters** — explain the problem it solves or the value it delivers
3. **How it works in practice** — provide a concrete usage context, workflow example, or scenario

This pattern applies regardless of content format. A blog post, a landing page, and a social post all benefit from this structure at the appropriate depth and length for the format.

**Avoid:**
- Describing features only by name without functional explanation
- Using adjectives ("powerful", "innovative", "smart") as substitutes for description
- Listing integrations or capabilities without explaining what they enable
- Implying capability without explaining the mechanism

When `CONTENT_BRIEF.content_points` includes specific features or integrations, generation skills can follow this explanatory pattern for each point.

---

## Hallucination Safety

Tech content carries elevated hallucination risk in the following areas. Generation skills may reference this guidance when applying source discipline:

- **Product features** — do not invent, assume, or extrapolate features not confirmed in the brief or provided context material
- **Integrations** — do not fabricate integration availability between specific products or platforms
- **Performance benchmarks** — do not generate benchmark figures, speed claims, or performance statistics unless supplied by the user
- **Adoption statistics** — do not invent user counts, customer numbers, or market share figures
- **Pricing** — do not state or imply pricing unless explicitly provided in the brief
- **Version-specific behaviour** — do not assert how a specific product version behaves unless confirmed in the source material

All technical claims must either come from the user via the brief or context material, or be clearly framed as general category behaviour rather than specific product claims.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe category behaviour (what this type of tool generally does) rather than asserting specific product behaviour that has not been confirmed.
