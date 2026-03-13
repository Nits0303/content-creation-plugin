
Global intake rules:

• Ask **one question at a time**  
• Skip any question whose signal already exists in the brief  
• Maximum six questions before generation begins  
• Context material extraction runs silently  
• User-provided material is treated as authoritative  
• If the user changes topic, brand, or format mid-conversation, discard the current brief and restart intake

---

# Pre-Phase 1 — Context Material Extraction

## Trigger condition

Fires when the user provides:

- a company website URL
- links to pages (About, Product, Services, Pricing)
- uploaded documents (PDFs, decks, brochures, internal docs)
- pasted marketing or website copy

If a URL is provided but cannot be accessed, ask the user to paste the relevant sections.

---

## Behaviour

Run extraction silently before asking any questions.

Do not summarise the extracted material unless the user explicitly asks.

Extracted signals populate the CONTENT_BRIEF and may skip Phase 1 entirely if sufficient information is available.

---

## Signals extracted

**Business Identity**

- Brand name → `brand_name`
- Industry or sector → `sector`
- Product or service description → `brand_description`

**Offer Specifics**

- Product features or services → `content_points`
- Problems the product solves → `angle` (partial)
- Use cases → `target_audience`

**Audience Signals**

- Who the offering is built for → `target_audience`

**Positioning**

- Differentiators or unique selling points → `brand_description` / `content_points`

**Social Proof**

- Testimonials, statistics, results → `content_points`

---

## Rules

- User material is treated as authoritative.
- Do not overwrite extracted information unless the user corrects it.
- If three or more signals are extracted, Phase 1 may be skipped.

---

# Phase 1 — Opening Question

## Trigger condition

Fires when:

- no context material was provided  
OR  
- context material populated fewer than three signals

---

## The question

> “What are we creating, what does your brand do, and who is this for?”

---

## Signals extracted

| Signal | CONTENT_BRIEF Field |
|------|------|
| Format (blog / webpage / social) | `format` |
| Subtype signal | `sub_type` (provisional) |
| Brand name | `brand_name` |
| Brand description | `brand_description` |
| Target audience | `target_audience` |
| Sector | `sector` (inferred) |
| Tone | `tone` (inferred) |
| Topic | `topic` |

If sector cannot be inferred, ask a single follow-up question:

> “What industry or space is your brand in?”

---

# Low-Signal Recovery Mode

## Trigger condition

Fires when user input contains:

- fewer than two usable signals
- no format signal

Example:

“write blog for my company”

---

## Behaviour

Send three numbered questions in a single message:

1. What does your business do?  
2. What type of content do you need — blog post, webpage, or social media?  
3. Who is this for?

This mode fires **once maximum per session**.

After the answers, return to the normal intake flow.

---

# Routing Checkpoint — Subtype Disambiguation

## Trigger condition

Fires when multiple subtypes remain plausible after Phase 1 or context extraction.

---

## Behaviour

Ask a structured multiple-choice question.

Example:

> “Should this help readers understand the topic, compare options, or walk through a curated list?”

If only one subtype remains plausible, skip the checkpoint.

---

# Phase 2 — Universal Brief Questions

Phase 2 questions depend on the detected format.

Questions are asked **one at a time** and skipped if the signal already exists.

---

## Blog Phase 2

1. Reader knowledge level  
2. Reader outcome  
3. Author intent  
4. Topic confirmation

These populate:

- `reader_knowledge_level`
- `reader_outcome`
- `content_goal`
- `topic`

---

## Webpage Phase 2

1. Visitor profile  
2. Primary action  
3. Funnel role  
4. Existing assets

These populate:

- `target_audience`
- `visitor_familiarity` (inferred)
- `cta_type`
- `content_goal`

If assets are provided during this phase, trigger context extraction.

---

## Social Phase 2

1. Platform destination → `platform`  
2. Content type → `sub_type`  
3. Duration or length target (Family 3 only) → `duration_target`

---

# Phase 3 — Dynamic Component Questions

Subtype-specific questions defined in:

- `skills/blog/SKILL.md`
- `skills/webpage/SKILL.md`
- `skills/social/SKILL.md`

The intake skill triggers the appropriate subtype question sequence.

---

# Phase 4 — SEO Layer

Fires only for blog and webpage content where search visibility matters.

Question asked:

> “What primary keyword should this content rank for?”

Populate:

`keyword`

If the keyword is weak or missing, run silent SERP analysis and infer a keyword cluster.

SEO layer is skipped for social content.

---

# Silent Layer — Brief Validation

Runs internally after Phase 3.

Purpose:

Ensure the CONTENT_BRIEF contains sufficient signals for generation.

If critical signals are missing, request the required information before proceeding.

The intake system must **never proceed to generation with an incomplete brief**.

---

# Orientation Statement

Runs immediately before router handoff.

One-line confirmation of the understood task.

Example:

> “Writing a how-to blog for [Brand] about [Topic] — here we go.”

No confirmation step. No bullet list.  
The user may interrupt if the interpretation is incorrect.

---

# CONTENT_BRIEF Population

Signals populate the CONTENT_BRIEF progressively during intake.

Fields include:

- brand_name
- brand_description
- target_audience
- sector
- topic
- angle
- content_goal
- reader_outcome
- tone
- keyword
- platform
- duration_target
- content_points
- cta_type
- reader_knowledge_level
- visitor_familiarity

Populate fields only when signals are confirmed.

Never overwrite user-provided information.

---

# Handoff to Router

Once the CONTENT_BRIEF is complete and the orientation statement has been delivered, pass control to:
