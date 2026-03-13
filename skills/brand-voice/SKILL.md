# Skill: Brand Voice
**File:** skills/brand-voice/SKILL.md
**Role:** Voice calibration layer. Translates CONTENT_BRIEF signals into writing voice rules for generation skills.

This skill does not generate content. It defines how the brand communicates so generation skills can apply that voice consistently throughout output.

---

## Pipeline Position

```
/create → intake → router → generation skill → humanise
```

Brand-voice is loaded by generation skills during generation as a voice calibration layer. It is not a sequential pipeline stage. It does not sit between the router and the generation skill — it is invoked internally by the generation skill at the point of writing.

---

## Inputs from CONTENT_BRIEF

Read these fields. Do not modify any of them.

| Field | Used for |
|---|---|
| `brand_name` | First-person / brand voice framing |
| `brand_description` | Understanding what the brand does and how it positions itself |
| `target_audience` | Audience register — who the content is written for |
| `tone` | Primary tone signal from intake — drives tone routing |
| `reader_knowledge_level` | Jargon tolerance and explanation depth |
| `sector` | Cross-references sector overlay rules — sector always overrides brand voice on compliance language |
| `content_goal` | Used in Tone Derivation Step 2 to resolve ambiguous sector-derived profiles |

If `tone` is empty: derive voice from `brand_description`, `target_audience`, and `sector` using the Tone Derivation Rules below. Do not ask for tone. Do not leave voice undefined.

---

## Tone Routing

Tone values are matched case-insensitively against the following defined profiles. Only these six values are valid:

| tone value | Voice Profile |
|---|---|
| `professional` | Professional |
| `friendly` | Friendly |
| `authoritative` | Authoritative |
| `casual` | Casual |
| `technical` | Technical |
| `persuasive` | Persuasive |

If `CONTENT_BRIEF.tone` does not match one of these six values — including when it is empty, null, or unrecognised — apply Tone Derivation Rules. Do not attempt fuzzy interpretation. Do not Hard Stop on an unrecognised tone value.

---

## Voice Profiles

Each profile defines behaviour across the five voice dimensions. Generation skills apply these rules during writing.

---

### Professional

**Formality level:** Mid-to-high. Formal enough to be taken seriously. Not so formal it creates distance.
**Authority level:** Confident. Statements are made directly. Hedging language minimised.
**Sentence density:** Balanced. Mix of short and medium sentences. Avoid run-on constructions.
**Jargon tolerance:** Moderate. Industry-standard terminology is acceptable. Unexplained acronyms and insider shorthand are not.
**Emotional intensity:** Low-to-neutral. The work and the results carry the persuasion — not enthusiasm signals.

Vocabulary preference: precise over vivid. Concrete over abstract. No filler adjectives.

---

### Friendly

**Formality level:** Low-to-mid. Conversational. Contractions permitted throughout.
**Authority level:** Peer-level. The brand knows its subject but speaks as a guide, not an expert above the reader.
**Sentence density:** Short-to-medium. Readable at pace. No long multi-clause sentences.
**Jargon tolerance:** Low. Plain language throughout. Technical terms explained on first use without condescension.
**Emotional intensity:** Warm. Acknowledgement of reader's situation. Enthusiasm permitted but not manufactured.

Vocabulary preference: everyday over formal. Direct over formal. Second-person ("you") throughout.

---

### Authoritative

**Formality level:** High. No contractions unless subtype register specifically requires them.
**Authority level:** Expert. The brand leads — it does not seek validation from the reader. Statements are declarative.
**Sentence density:** Medium-to-dense. Arguments are developed. Short sentences reserved for emphasis, not default.
**Jargon tolerance:** High. Sector-appropriate technical language is used without definition when audience is intermediate or advanced.
**Emotional intensity:** Low. Authority is conveyed through precision and specificity — not through enthusiasm or urgency.

Vocabulary preference: exact and specific. No approximations. No hedging chains.

---

### Casual

**Formality level:** Low. Colloquial constructions permitted. Contractions throughout.
**Authority level:** Conversational peer. The brand has an opinion but shares it as a conversation, not a lecture.
**Sentence density:** Short. Punchy. One idea per sentence is the default.
**Jargon tolerance:** Minimal. Plain language. If technical terminology appears it must feel earned and explained.
**Emotional intensity:** High relative to other profiles. Personality is visible. Dry humour permitted where subtype allows.

Vocabulary preference: plain, direct, energetic. Avoid anything that sounds like a press release.

---

### Technical

**Formality level:** Mid-to-high. Register is precise, not warm.
**Authority level:** Expert peer. Assumes reader competence. Does not over-explain foundational concepts.
**Sentence density:** Dense where precision requires it. Short sentences for procedural steps. Longer constructions for explanation.
**Jargon tolerance:** High. Technical terminology is used correctly and without apology for audiences at intermediate or advanced level. For beginner audiences: jargon is defined on first use.
**Emotional intensity:** Neutral. Precision is the priority. Enthusiasm signals do not add credibility in technical content.

Vocabulary preference: accurate over accessible. Specific over general. Functional over decorative.

---

### Persuasive

**Formality level:** Variable — follows sector and subtype. Persuasion does not require formal or informal register specifically.
**Authority level:** Confident and benefit-led. Outcomes stated first. Mechanism second. Claims backed by proof where available.
**Sentence density:** Varied. Short sentences for impact beats. Longer constructions for building the case.
**Jargon tolerance:** Audience-calibrated. Plain language for general audiences. Sector terminology for specialist audiences.
**Emotional intensity:** Elevated. Stakes are made explicit. Reader's situation acknowledged before the solution is presented.

Vocabulary preference: outcome words over feature words. Action verbs over noun stacks. Specifics over generalities.

---

## Voice Dimension Rules

These five dimensions apply across all profiles. Generation skills calibrate each one against the active voice profile.

### Formality Level

Three positions: `authoritative` | `professional` | `casual`

Formality governs: contraction use, sentence opener conventions, pronoun choices (we / you / one), and whether the brand speaks as leader or peer.

- Authoritative: no contractions, declarative constructions, brand speaks as leader
- Professional: contractions permitted sparingly, balanced register
- Casual: contractions throughout, brand speaks as peer, first-name register acceptable

### Authority Level

Two positions: `confident-expert` | `conversational-guide`

Confident expert: statements are made directly. "This is how it works" not "This might be one way to think about it."
Conversational guide: shares expertise but invites the reader. Acknowledges that the reader has context and agency.

### Sentence Density

Two positions: `short-punchy` | `layered-explanation`

Short-punchy: one idea per sentence default. Paragraphs of 2–3 sentences. Used when the reader needs to move fast or act quickly.
Layered-explanation: ideas develop across sentences. Paragraphs may run 3–4 sentences. Used when the reader is evaluating, learning, or being persuaded by argument rather than by brevity.

### Jargon Tolerance

Calibrated against `reader_knowledge_level` from CONTENT_BRIEF.

| reader_knowledge_level | Jargon tolerance |
|---|---|
| `beginner` | Low. All technical terms defined on first use. No assumed knowledge. |
| `intermediate` | Moderate. Sector-standard terms used without definition. Niche or advanced terms defined on first use. |
| `advanced` | High. Technical language used at full register. Definitions only for terms outside standard expertise. |

If `reader_knowledge_level` is null: default to `intermediate`. Do not ask.

### Emotional Intensity

Three positions: `neutral` | `warm` | `elevated`

Neutral: no enthusiasm signals, no urgency language, no emotional appeals. Facts and specifics carry the weight.
Warm: acknowledgement of reader's situation, human-first framing, moderate enthusiasm appropriate.
Elevated: stakes made explicit, urgency where real (not manufactured), emotional resonance built through specifics not through adjectives.

---

## Tone Derivation Rules

Applies when `CONTENT_BRIEF.tone` is empty, null, or unrecognised.

Derive the voice profile from the following signals in order of priority:

**Step 1 — Sector signal**

`CONTENT_BRIEF.sector` contains canonical sector IDs. Match against the following table.

| sector (canonical ID) | Default derived profile |
|---|---|
| `legal-ca-finance` | Professional |
| `healthcare` | Professional |
| `it-saas` | Technical or Professional (depends on audience) |
| `education` | Friendly or Professional (depends on audience age/role) |
| `ecommerce` | Friendly or Persuasive (depends on content_goal) |
| `hospitality-travel` | Friendly |
| `marketing-agency` | Persuasive or Professional |
| `hr-recruitment` | Professional |
| `manufacturing-b2b` | Technical or Authoritative |
| `local-services` | Friendly |
| `general` | Professional (default fallback) |

**Step 2 — content_goal signal**

If sector derivation produces two candidates, use `content_goal` to resolve:

| content_goal | Resolves toward |
|---|---|
| `convert` | Persuasive |
| `educate` | Friendly or Technical (depends on reader_knowledge_level) |
| `build-authority` | Authoritative |
| `inform` | Professional |
| `entertain` | Casual |
| `engage` | Friendly |
| `persuade` | Persuasive |

**Step 3 — B2B vs B2C signal**

If derivation is still ambiguous: B2B signals → Professional or Authoritative. B2C signals → Friendly or Casual. B2C + conversion intent → Persuasive.

Generation skills may optionally reference the active voice profile when composing the orientation statement. Brand-voice does not control, write, or modify the orientation statement — that is produced by the intake system before generation begins.

---

## Voice Override Rules

These rules take precedence over the active voice profile when they fire.

**Interview subtype:** Tone is dictated by the interviewee's register, not the brand's. The interviewer's framing prose matches brand voice. The interviewee's responses preserve their own voice — directness, technical language, informality — exactly as provided. Brand voice applies only to Claude-authored framing.

**Guest Post subtype:** The voice belongs to the named guest author, not the host platform. If the author is a specific named individual, their personal voice takes precedence over brand voice. If the author is the brand or anonymous, brand voice applies.

**Opinion subtype:** Personal voice vs brand voice is determined by intake Q3. Personal: first-person, more direct, author's stance forward. Brand: "we" voice, authoritative, collective position.

**Case Study subtype:** Brand voice applies to framing prose only. Analytical register is preserved throughout — no warmth injection, no enthusiasm signals on results sections.

---

## Sector Interaction Rules

Sector overlays are loaded at `sectors/{sector_id}/SKILL.md`.

**Rule:** Sector compliance rules override brand voice without exception.

Brand voice must not:
- Remove or soften disclaimers required by sector overlay
- Override compliance language in Legal, Healthcare, or Financial sector content
- Remove credentialing requirements on YMYL content
- Adjust the legal register of Privacy/Terms pages regardless of brand tone

Where sector overlay requires a specific register (e.g. legal disclaimer, medical advisory), that register applies to that content block only. The rest of the content continues in the active brand voice profile.

---

## Humanise Interaction Rules

The humanise skill runs after generation and performs linguistic refinement.

Brand-voice must not duplicate humanise tasks. Specifically:

- Brand-voice does not check or remove banned phrases — that is humanise
- Brand-voice does not enforce rhythm variation — that is humanise
- Brand-voice does not apply readability scoring — that is humanise
- Brand-voice does not split overly long sentences — that is humanise

Brand-voice defines the intended voice register before generation. Humanise refines the language quality after generation. The two skills operate on different concerns and must not overlap.

---

## Prohibited Behaviours

This skill must never:

1. Ask questions
2. Trigger intake or re-run any intake phase
3. Modify `CONTENT_BRIEF` fields in any way
4. Generate content sections, headings, or copy
5. Perform SEO behaviour
6. Run web search
7. Modify CTA wording
8. Modify headings
9. Change section order
10. Override sector compliance rules
11. Apply humanise-scope transformations (banned phrase cleanup, rhythm variation, readability scoring)
12. Invent tone values not derivable from CONTENT_BRIEF signals
13. Leave voice undefined — if tone cannot be determined, apply the derivation rules and proceed
