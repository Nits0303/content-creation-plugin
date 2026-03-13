# Skill: Humanise
**File:** skills/humanise/SKILL.md
**Role:** Linguistic refinement of generated content. Runs after generation. Does not generate content.

---

## Pipeline Position

```
/create → intake → router → blog / webpage / social → humanise ← you are here
```

Input: generated content + CONTENT_BRIEF + HUMANISE_PASS metadata
Output: refined content — structure identical, language improved

---

## What This Skill Does and Does Not Do

**Does:**
- Improve sentence rhythm and natural flow
- Remove robotic phrasing and banned phrases
- Break overly long sentences
- Reduce filler transitions
- Improve paragraph readability

**Does not:**
- Generate new content
- Add information not already present
- Strengthen or weaken claims
- Change meaning of any sentence
- Invent examples
- Ask intake questions
- Re-run generation
- Add commentary to the output

---

## Preservation Rules — Absolute

These elements must never be modified under any HUMANISE_PASS value. If an edit accidentally touches any of the following, revert before returning output.

| Element | Rule |
|---|---|
| Section order | Unchanged |
| Headings | Unchanged — wording, level, placement |
| CTA wording | Unchanged — exact copy preserved |
| Statistics and numbers | Unchanged — no rounding, rewording, or repositioning |
| Citations and sources | Unchanged |
| Quoted material | Unchanged — customer quotes, testimonials, interview responses, expert attributions |
| Placeholder markers | Unchanged — `[Include your own source here...]` and all similar markers pass through untouched |
| Platform formatting | Unchanged — slide breaks, post units, thread numbering, hashtag placement |
| Character limits | Unchanged — no edit may cause a unit to exceed its platform character ceiling |
| Subtype structural blocks | Unchanged — Event Details Block, Attribution Block, Scene Direction Layer, and all other named structural components pass through intact |

---

## HUMANISE_PASS Values

HUMANISE_PASS is set by the generation skill before handoff. Read it from the metadata. Do not determine it here.

Valid values: `full` | `restricted` | `quote-protected`

If HUMANISE_PASS is missing or invalid — return the content unchanged and flag the missing metadata.

---

## Full Pass

### Allowed transformations

**Sentence rhythm variation**
No three or more consecutive sentences of the same approximate length. Vary sentence structure across short, medium, and longer constructions naturally. Do not force variation — only apply where the original rhythm is mechanically uniform.

**Remove robotic phrasing**
Identify and rewrite sentences that read like system-generated output: overly formal constructions, passive voice where active voice is more natural, hedging chains ("it is important to understand that in order to..."), and hollow sentence openers that add no information before the main clause.

**Passive voice check**
Where passive voice is unnecessary and an active construction preserves the meaning more naturally, prefer the active voice. If passive voice serves a deliberate editorial purpose, preserve it. Do not enforce quantitative limits.

**Readability standard**
Flesch score target: 60–70 for general audience content. 50–60 for professional or sector-specific content. Apply this as a calibration guide, not a mechanical filter.

**Paragraph length**
Maximum 4 sentences per paragraph for digital content. If a paragraph exceeds this — split at the most natural conceptual boundary. Do not split mid-argument.

**Replace generic transitions**
Banned transitions (see Banned Phrase Cleanup section) must be replaced with natural connective language or removed entirely where the connection is already implied by sequence.

**Shorten verbose phrases**
Identify phrases where the same meaning can be expressed in fewer words without loss. Shorten them. Do not remove meaning — compress it.

---

## Restricted Pass

### What applies

- Grammar correction — fix grammatical errors only. Do not restructure to achieve better grammar.
- Punctuation fixes — correct punctuation errors. Do not rewrite to avoid a punctuation challenge.
- Banned phrase cleanup — apply the banned phrase list. Replace banned phrases with the closest natural equivalent that preserves the original register. Do not use the replacement as an opportunity to improve the sentence beyond fixing the banned phrase.
- Passive voice check — where passive voice is unnecessary and an active construction preserves the meaning more naturally, prefer the active voice. If passive voice serves a deliberate editorial purpose, preserve it. Do not enforce quantitative limits.
- Paragraph length check — apply the 4-sentence maximum. Split only at natural conceptual boundaries.
- Basic rhythm variation — break runs of three or more sentences with identical structure. Minimal intervention only.

### What does not apply

- Opinion injection — banned. Analytical register is preserved exactly as written.
- Tone softening — banned. Professional register is preserved exactly as written.
- Narrative rewriting — banned. Structure and sequence are not altered.
- Sentence-level rewrites beyond grammar and banned phrase scope — banned.

**Restricted pass example:**
Original (correct): "The campaign improved retention by 23%."
Full pass violation: "What stood out most was the dramatic 23% improvement in retention."
Restricted pass output: original sentence unchanged.

---

## Quote-Protected Pass

### What applies

All Full Pass standards apply to framing prose — sentences written by Claude, not sourced from a person.

### What does not apply to quoted material

Quoted material is never touched. The following are absolute prohibitions on any text inside quotation marks or presented as a direct attribution:
- No shortening
- No rewriting
- No smoothing
- No rhythm adjustment
- No grammar correction
- No register improvement

The interviewee's or customer's speech patterns — fragments, directness, informality, run-ons — are preserved exactly as provided.

---

## Banned Phrase Cleanup

Applies under all three pass values. Banned phrases must be replaced or removed regardless of HUMANISE_PASS level.

**Category 1 — Generic AI-isms**
In today's digital/fast-paced landscape, In the modern era, In the ever-evolving world of, It's important to remember that, It's important to note that, Unlock the secrets, Unleash your potential, Harness the power of, A testament to, Dive deep into, Delve into, At its core, At the end of the day, Comprehensive guide, Think of it as

**Category 2 — Forbidden Transitions**
Furthermore, Moreover, In addition, Consequently, Additionally, Subsequently, Accordingly, Indeed, Not only...but also, In conclusion, To summarise, It goes without saying, Last but not least

**Category 3 — Lazy Adjectives**
Revolutionary, Game-changing, Cutting-edge, Robust, Seamless, Synergy, Pivotal, Transformative, Innovative, Groundbreaking, Dynamic, Powerful, Vibrant, Crucial role, Ever-evolving, Nuanced approach

**Category 4 — Corporate Verbs**
Leverage (use: use), Facilitate (use: help), Utilize (use: use), Endeavour, Underscore (use: show/prove), Optimize (use: improve), Revolutionize, Empower, Foster, Navigate (metaphorical), Circle back, Deep dive, Move the needle, Streamline

### Replacement rules
- Replace with natural language that carries the same meaning.
- Under Restricted Pass: replacement must match the register of the surrounding text exactly — do not upgrade the register while fixing the banned phrase.
- Under Quote-Protected Pass: if a banned phrase appears inside quoted material — leave it. The prohibition applies to Claude-authored text only.

---

## Rhythm Rules

Apply under Full Pass and Quote-Protected Pass (framing prose only).

- No three or more consecutive sentences beginning with the same word or phrase.
- No three or more consecutive sentences of the same approximate length (±5 words).
- No mechanically uniform paragraph structure where every paragraph follows the same sentence-count pattern.
- No lists of three or more items where every item is identically structured. At least one item should vary in construction.

Variation must emerge from natural language choices, not from artificial restructuring that changes meaning.

---

## Readability Rules

Apply under Full Pass and Quote-Protected Pass (framing prose only).

- Split sentences over 35 words where a natural split point exists. Do not split if splitting would break a single continuous thought.
- Remove filler transitions that add no logical connection (see Banned Phrase Cleanup).
- Ensure paragraphs do not become dense unbroken blocks. Maximum 4 sentences per paragraph enforced.
- Tighten verbose wording: identify phrases using more words than necessary to express the idea — compress without changing meaning.

---

## Pre-Output Validation

Run this check before returning any output. Never user-facing. Internal only.

- [ ] Section order matches input exactly
- [ ] All headings unchanged — wording and level
- [ ] All CTA copy unchanged
- [ ] All statistics and numbers unchanged
- [ ] All citations and source references unchanged
- [ ] All quoted material unchanged
- [ ] All placeholder markers present and unchanged
- [ ] Platform formatting preserved (slide breaks, units, hashtag placement, numbering)
- [ ] Character limits still satisfied per post/unit (Thread, Story, Carousel)
- [ ] All structural blocks intact (Event Details Block, Attribution Block, Scene Direction Layer, etc.)
- [ ] No opinion or claim injected that was not present in the input
- [ ] No example invented that was not present in the input
- [ ] Meaning of every sentence preserved

If any check fails — revert the affected edit to original wording. Do not attempt a second rewrite. Revert and return.

---

## Output Rule

Return the transformed content only.
No preamble. No commentary. No explanation of what was changed.
The output is the refined content, ready for delivery to the user.
