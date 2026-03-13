# Skill: Repurpose
**File:** skills/repurpose/SKILL.md
**Role:** Create a new CONTENT_BRIEF for a different format by inheriting identity and topic signals from an existing brief. Does not restart intake. Does not reproduce the original content.

---

## Purpose

Repurpose converts existing content into a new format — blog to social, webpage to blog, social post to long-form — without discarding the brand identity, audience, and topic signals that defined the original.

It creates a new CONTENT_BRIEF with inherited fields from the source brief, then routes execution through the standard generation pipeline for the target format.

Repurpose is not a copy operation. The new format requires new structure, new components, and new generation. The inherited fields provide continuity of identity and topic — not continuity of content.

---

## Pipeline Position

```
/create → intake → router → generation skill → humanise → [content delivered]
                                                                    ↓
                                                           /repurpose ← you are here
                                                                    ↓
                                          new CONTENT_BRIEF → router → generation skill → humanise
```

Repurpose is triggered after content has been delivered. It does not sit inside the generation pipeline — it creates a new brief and re-enters the pipeline at the router.

---

## Command Trigger

Activated by: `/repurpose`

---

## Inputs

Repurpose receives three inputs:

1. **Existing CONTENT_BRIEF** — the brief that produced the original content
2. **Previously generated content** — the full output delivered to the user
3. **User's repurpose instruction** — the target format and any additional signals

Examples of valid instructions:
- "turn this into a LinkedIn post"
- "repurpose this as an Instagram carousel"
- "convert this blog into a landing page"
- "make a Twitter thread from this article"
- "repurpose this as a short video script"

---

## New CONTENT_BRIEF Construction

Repurpose creates a new CONTENT_BRIEF. Intake does not restart.

### Inherited fields

These fields carry over from the source brief unchanged:

| Field | Rationale |
|---|---|
| `brand_name` | Brand identity is constant across formats |
| `brand_description` | Brand identity is constant across formats |
| `target_audience` | The audience does not change with format |
| `tone` | Brand voice carries across formats |
| `sector` | Sector context does not change with format |
| `topic` | The subject matter is the same |
| `angle` | The editorial position carries across formats |

### Changed fields

These fields are reset and determined by the target format:

| Field | How it is set |
|---|---|
| `format` | Determined from the user's repurpose instruction |
| `sub_type` | Determined from the user's repurpose instruction or Routing Checkpoint |
| `length_tier` | Recalculated using the natural range of the target subtype as defined in the generation skill. If the user explicitly requests a target length, that instruction overrides the default range. Otherwise select the natural range midpoint. |
| `output_format` | Inherited from source brief unless the new format implies otherwise. `script` applies only to Short Video Script and Long Video Script. `cms-ready` applies only when user signals a CMS destination. |

### Not inherited

Repurpose may inherit only the fields explicitly listed in the Inherited fields table above. All other CONTENT_BRIEF fields must be recalculated or left unset until the generation skill populates them. This prevents accidental inheritance if the schema expands in future.

Fields that require explicit recalculation for the new format include `keyword`, `content_points`, `cta_allowed`, and `cta_type`, which are format-specific and must not be assumed to transfer. Both CTA fields must be recalculated by the generation skill for the target format — CTA mechanics differ by format and subtype. These are illustrative — the rule applies to all fields not in the Inherited table, not only these four.

### Social-specific fields

For repurpose targets that are social formats, these fields must also be set fresh:

| Field | Set from |
|---|---|
| `platform` | User's repurpose instruction or Social Phase 2 Q1 if not stated |
| `unit_count` | Recalculated for target subtype (Family 2 only) |
| `arc_type` | Recalculated for target subtype (Family 2 and 3 only) |
| `duration_target` | Recalculated for target subtype (Family 3 only) |
| `delivery_mode` | Recalculated (Short Video Script only) |
| `article_type` | Recalculated (LinkedIn Article only) |

---

## Target Format Determination

Format inference must map only to the three canonical formats defined in the architecture: `blog` | `webpage` | `social`.

If the user names a subtype directly — for example "Instagram carousel", "YouTube script", "LinkedIn article", "landing page" — repurpose must map the instruction to the correct canonical format and subtype:

| User instruction example | format | sub_type |
|---|---|---|
| "Instagram carousel" | `social` | `carousel` |
| "YouTube script" | `social` | `long-video-script` |
| "Twitter thread" | `social` | `thread` |
| "LinkedIn post" | `social` | `text-post` |
| "LinkedIn article" | `social` | `linkedin-article` |
| "Reels script" | `social` | `short-video-script` |
| "Landing page" | `webpage` | `landing-page` |
| "Blog post" | `blog` | resolved via Routing Checkpoint |

This ensures the router always receives a valid `format` value, never a raw subtype string.

If the user's instruction clearly identifies the target format and subtype — proceed directly.

If the target format is clear but the subtype is ambiguous — apply the Routing Checkpoint: present the plausible subtype options and ask the user to confirm before constructing the new brief.

If the target format is not stated — ask one question before proceeding:
> "What format would you like to repurpose this into — blog post, webpage, or social media content?"

Do not ask more than one question to determine the target. Do not restart intake.

---

## Content Derivation Rules

Repurpose constructs a new CONTENT_BRIEF and routes execution to the appropriate generation skill. The generation skill performs the content generation according to the target format's architecture. Repurpose does not generate content directly — it does not copy the original content into the new format.

**Source content is reference only, not an authoritative factual source.**
The previously generated content may be used to understand the structure, emphasis, and argument of the original — but it must not be treated as a primary factual source. Previously generated content may include Intelligent Extension Zone material produced during generation that contains web-sourced additions not present in the original brief. Carrying those elements forward without re-verification would bypass F.0.3.

Factual elements carried into the new content must come from:
- The source CONTENT_BRIEF
- User-provided context material
- Confirmed zone material
- Verified sources retrieved under F.0.3 in the current session

**What may be drawn from the source content (as reference):**

- Core argument, insight, or thesis — adapted to the new format's structure
- Confirmed zone facts already present in the source CONTENT_BRIEF — statistics, claims, and proof elements the user explicitly provided
- Specific examples or case details — only those explicitly provided by the user in the original brief or context material, not those generated via web search during original generation

**What must not be carried over:**

- Sections that do not translate to the target format's component library
- SEO structure (headings calibrated for a keyword, internal link placeholders, meta outputs) — not carried into social or non-SEO formats
- Word-for-word copy from the original — repurpose is a format transformation, not a copy-paste
- Intelligent Extension Zone material from the original generation unless it can be re-verified under F.0.3 in the current session

**Confirmed zone precedence:**
When `CONTENT_BRIEF.context_material_used = true` in the source brief: all brand details extracted during Pre-Phase 1 remain confirmed zone content in the new brief. No re-verification required.

---

## Hallucination Safeguards

F.0.3 rules apply to all repurpose operations without exception. Changing format does not relax hallucination standards.

Repurpose must not introduce:
- Fabricated statistics or data not present in the source content or confirmed zone
- Fabricated or attributed quotes
- Synthetic research claims ("studies show", "experts agree", "recent data suggests")
- Named sources that cannot be traced to confirmed zone content or a current-session web search source

**Sourcing rule for repurpose:**
Repurpose reuses verified material already present in the source content and confirmed zone. If the target format requires components that need sourced material not present in the source content — apply the F.0.3 Source Verification Chain. Repurpose does not introduce a separate research system. It uses the same verification rules defined for generation skills.

If a required component cannot be sourced: drop it or insert a placeholder — `[Include your own source here — no verified source available for this component]`.

---

## Structural Rules by Target Format

Repurpose must respect the target format's component library and structural rules as defined in the relevant generation skill.

- Target is blog → apply `skills/blog/SKILL.md` subtype rules
- Target is webpage → apply `skills/webpage/SKILL.md` subtype rules
- Target is social → apply `skills/social/SKILL.md` subtype rules

Repurpose does not define subtype architecture. It defers to the generation skill for the target format.

Core components for the target subtype must be present in the repurposed output. If the source content does not contain sufficient material to populate all Core components for the target subtype — flag the gap before generating.

---

## Sector Overlay

The inherited `sector` field means the sector overlay for the target format is already known. Load `sectors/{sector_id}/SKILL.md` as part of the generation step for the new format.

Sector compliance rules apply to the repurposed content exactly as they apply to originally generated content. Repurposing does not exempt any output from YMYL requirements, disclaimer rules, or credentialling requirements.

---

## Humanise Layer

After generation of the repurposed content is complete, the output passes through `skills/humanise/SKILL.md`.

HUMANISE_PASS is determined fresh for the target subtype — not inherited from the source content. The generation skill for the target format assigns HUMANISE_PASS before handoff to humanise.

---

## Output Rules

Repurpose returns the fully generated content for the target format.

- Output the complete new content — not a summary of changes, not a diff.
- Do not explain what was carried over or what was changed.
- Do not add preamble or commentary.
- Return the content ready for delivery to the user.

---

## Prohibited Behaviours

This skill must never:

1. Restart intake or ask intake questions
2. Carry `keyword`, `content_points`, `cta_allowed`, or `cta_type` from the source brief into the new brief
3. Copy source content word-for-word into the new format
4. Carry SEO structure (keyword-calibrated headings, internal links, meta outputs) into non-SEO target formats
5. Generate content for the target format before confirming the target subtype when ambiguity exists
6. Relax F.0.3 hallucination safeguards because the format has changed
7. Fabricate statistics, quotes, or synthetic authority claims
8. Carry `platform`, `unit_count`, `arc_type`, `duration_target`, `delivery_mode`, or `article_type` from a social source brief unless the target format uses the same field
9. Assign HUMANISE_PASS from the source content — it must be recalculated for the target subtype
10. Output a diff, summary, or change log instead of the complete repurposed content
