# Blog Generation Skill

**Role:** Generate blog content from a completed CONTENT_BRIEF. Intake is already done. Phase 3 subtype questions are already answered. This skill reads the brief and generates.

**Do not ask intake questions. Do not re-run Phase 1–4. Generate.**

---

## Pipeline Position

```
/create → intake/SKILL.md → router/SKILL.md → blog/SKILL.md ← you are here
```

Input: populated CONTENT_BRIEF  
Output: completed blog post in CONTENT_BRIEF.output_format

---

## CONTENT_BRIEF Contract

This skill assumes a fully populated CONTENT_BRIEF produced by the intake system. Generation must halt if required fields are missing.

**Required fields:**
- format
- sub_type
- topic
- angle
- content_goal
- target_audience
- reader_knowledge_level
- sector
- length_tier
- output_format

**Optional fields:**
- brand_name
- brand_description
- keyword
- tone
- hallucination_risk_components
- context_material_used

If any required field is missing → Hard Stop before generation.

---

## Pre-Generation Sequence

Before writing any content, run these steps in order:

**Step 1 — Confirm brief sufficiency.** Run the Silent Layer check (see below). If the brief fails, fire the Hard Stop before writing a single word.

**Step 2 — Load sector skill.** Read `sectors/{sector_id}/SKILL.md` before generating. Sector overlay behaviour applies throughout.

`sector_id` must be resolved from the following canonical mapping. Do not derive it programmatically — use this table only:

| CONTENT_BRIEF.sector | sector_id |
|---|---|
| IT / SaaS / Tech | `tech` |
| Legal / CA / Finance | `ca_legal-finance` |
| Healthcare | `healthcare` |
| Real Estate | `real-estate` |
| Education | `education` |
| E-commerce / Retail | `ecommerce-retail` |
| Hospitality / Travel | `hospitality-travel` |
| Marketing / Agency | `marketing-agency` |
| HR / Recruitment | `hr-recruitment` |
| Manufacturing / B2B | `manufacturing-b2b` |
| Local Services | `local-services` |
| general | no sector skill loaded — proceed without overlay |

If `sector_id = general`: skip sector overlay behaviour entirely. Continue generation using base subtype rules only.

**Step 3 — Determine humanise pass.** Identify the required HUMANISE_PASS for this subtype. Valid values: `full` | `restricted` | `quote-protected`. Attach HUMANISE_PASS metadata to the output. The actual transformation is performed by `skills/humanise/SKILL.md`.

**Step 4 — Identify hallucination-risk components.** Check CONTENT_BRIEF.hallucination_risk_components. Apply F.0.3 rules to every component flagged.

**Step 5 — Route to subtype.** Use CONTENT_BRIEF.sub_type to select the correct generation rules below.

The router directs execution based on CONTENT_BRIEF.format only. Subtype routing is handled internally within this skill using CONTENT_BRIEF.sub_type.

**Format Validation.** If `CONTENT_BRIEF.format ≠ "blog"` → Hard Stop. This skill only processes blog format requests.

---

## Silent Layer — Brief Sufficiency Check

Run before generating. Never user-facing unless Hard Stop fires.

| Length Tier | Minimum Material Required |
|---|---|
| 800–1200 words | Topic + angle + 1 example or data point |
| 1200–1800 words | Above + 2–3 specific content points |
| 1800–2500 words | Above + at least 1 of: quote / case detail / stat + specific CTA context |
| 2500+ words | All of above + brand story element OR expert input OR research reference |

**Hard Stop — non-negotiable.** If the brief is insufficient for the requested length, respond:

> "Before I start writing, I need to flag something. For a [X]-word [subtype] I need [specific missing material] — without that I'd be making it up. Three options: 1. Give me [specific ask]. 2. I can write a strong [shorter realistic length] piece with what I have now. 3. [Eligible subtypes only] I can draft this using researched material — you'll need to review and add your brand's specific perspective before publishing."

Option 3 (research-based generation) is available **only** for: SEO-Focused Article, Beginner's Guide, Ultimate Guide, News & Updates, Listicle (market curation).

Option 3 is **never** available for: Case Study, Interview, Opinion, Product Review (own product), Guest Post.

**Research Mode Flag.** If Option 3 is selected: set `CONTENT_BRIEF.research_mode = true`. Generation proceeds using web-researched material in the Intelligent Extension Zone only. This flag keeps the behaviour auditable — downstream skills can check it to apply appropriate hallucination safeguards.

---

## F.0 — Cross-Subtype Principles

Apply to every blog subtype without exception.

### Confirmed Zone / Intelligent Extension Zone

- **Confirmed Zone:** everything the user explicitly provided or confirmed. Written with full confidence, no hesitation.
- **Intelligent Extension Zone:** Claude extends with relevant, web-searched and verified supporting material — external evidence, expert reactions, contextual background, forward-looking developments.
- User-provided material from Pre-Phase 1 context extraction is confirmed zone — authoritative by definition.
- When CONTENT_BRIEF.context_material_used = true: treat all brand details as confirmed zone.
- If something feels like a stretch, it does not go in.

### Soft Ceiling with Open Slot

No hard blocks on length. When the user requests above the natural range for a subtype, state the standard range and ask what the user wants to add. The open slot is filled only with user-provided substance. No padding. No fabrication.

### Community Relevance Block

Eligible across all 12 subtypes. Web search fills it when user skips. If web search returns nothing useful — the section is dropped entirely. Never fabricated.

### Length is an Output, Not a Target

Write to the natural range when the user does not specify. Component priority ranking determines what gets dropped for shorter pieces. Never pad, stretch, or fabricate content to hit a word count.

### Hard Stop Rule

Generation must never proceed when the CONTENT_BRIEF is insufficient.

The validation logic and response format are defined in the Silent Layer — Brief Sufficiency Check section above.

### Web Search Native

Cowork supports web search natively. Use web search for: community research, SERP pattern detection, competitor data, expert quotes, pricing verification, and any factual material the user has not provided.

**Confirmed Zone Precedence Rule.** Web search may only populate missing supporting material. User-provided facts in the Confirmed Zone are never overridden or contradicted by web search results. If a web search result conflicts with user-provided information — the user-provided information stands. Flag the conflict to the user if it is material.

**Web Search Failure Behaviour:** If web search fails or produces no credible sources:
- Drop the component entirely.
- Never fabricate statistics, research claims, or quotes.
- Never approximate missing data.
- Where applicable, insert a placeholder: `[Include your own source here — web search returned no credible results for this component]`.

**Placeholder Safety Rule.** Placeholders are allowed during drafting only. Before final output: if any placeholder remains in the content — notify the user and do not publish that section. Placeholders must never appear in CMS-ready or final published output.

### Global Section Maximum

Default maximum 8 H2 sections for all blog subtypes unless the subtype defines its own ceiling. Prevents structural bloat. Never add a section without a clear reason. If 8 H2 sections are reached and content remains — consolidate, do not add.

Exceptions: F.4 News & Updates = 6 section maximum. F.12 Ultimate Guide = 10 advisory (not a hard block).

---

## F.0.1 — Priority Tier System

Every component in every subtype is assigned one of four tiers.

| Tier | Definition | Behaviour |
|---|---|---|
| **Core** | Must appear in every piece of this subtype | Missing info → ask before generating. Never dropped. |
| **Conditional Core** | Mandatory only when a specific signal is detected | Behaves like Core when signal fires. Absent when signal is not present. |
| **Supporting** | Appears when information exists or length justifies it | No aggressive asking. Subject to selection cap. |
| **Enrichment** | Appears only when piece is long or user provides extra material | Dropped first when length is short. |

**Supporting Component Selection Cap — maximums, not targets:**

| Piece Length | Max Supporting Components |
|---|---|
| 800–1200 words | 1 maximum |
| 1200–1800 words | 2 maximum |
| 1800–2500 words | 3 maximum |
| 2500+ words | 4 maximum (only when sufficient material exists) |

**Conflict Resolution Rule:** When two components serve a similar reader need, the lower-tier one is evaluated first. Never stack two components that produce visible repetition.

---

## F.0.2 — Sector Overlay Behaviour

Load the relevant sector skill file. The table below defines auto-behaviours that apply on top of subtype rules.

| Sector | Key Auto-Behaviours |
|---|---|
| IT / SaaS / Tech | Technical depth calibrated to audience. Schema: Article or HowTo. YMYL not triggered. |
| Legal / CA / Finance | YMYL. Disclaimer auto-added. E-E-A-T critical. Author credentials mandatory. Add 'This is a personal/professional perspective, not legal/financial advice' framing for opinion content. |
| Healthcare | YMYL. Medical disclaimer mandatory. Anonymous author route unavailable. 'When to seek professional guidance' signal mandatory in Beginner's Guide challenges block. |
| Real Estate | Location specificity mandatory. Local SEO fires automatically. |
| Education | Board/curriculum specificity asked. Outcome-focused framing. Age and role calibration active. |
| E-commerce / Retail | Product-first framing. Pricing accuracy critical. Soft CTA rule applies strictly for Beginner's Guide. |
| Hospitality / Travel | Destination specificity. Local SEO fires. Seasonal framing optional. |
| Marketing / Agency | Results-first. Statistics must be sourced. 'Results may vary' qualifier on any client metrics. |
| HR / Recruitment | Role specificity. Compliance framing for legal regions. |
| Manufacturing / B2B | Technical depth. Decision-maker framing. Jargon permitted. |
| Local Services | Local SEO fires automatically. City/suburb specificity mandatory. Trust-first tone. Service radius framing. |

---

## F.0.3 — Hallucination Window Rules

Applies system-wide. The hallucination window opens in the intelligent extension zone — when Claude populates components requiring supporting information not provided by the user.

**Risk classification:**

High-risk components — all three rules apply:
- Authority Quote Block / Expert Quote Block
- Data / Statistics Block
- Research Findings sections
- Common Misconceptions Block

Medium-risk components — Rules 2 and 3 apply:
- Industry Context Block
- Community Relevance Block
- What Happens Next Block (News)

When a component entry below carries **[F.0.3]**, all applicable rules govern how that component is populated.

### Rule 1 — The Source Verification Chain

For any component producing a factual claim sourced from outside the user's brief: Claude must be able to trace the claim to a specific URL or named publication retrieved in the **current session's web search**. If the source cannot be traced to the current session — the claim does not go in. Training data recall is not a citable source in this plugin.

### Rule 2 — The Grey Zone Drop Rule

When web search returns adjacent, outdated, low-quality, or conflicting results for a section requiring specific factual support — drop the section entirely rather than using adjacent material.

Exception: Claude may include the section as a clearly marked placeholder — `[Include your own source here — I found conflicting data on this point]` — so the user knows the gap exists.

### Rule 3 — The Synthetic Authority Signal

Hard stop during generation for these patterns without a traceable current-session source:
- "According to recent research..." without a traceable URL
- "Studies show..." without a named study
- "X% of [audience]..." without a traceable source
- "Experts agree..." without named experts
- "Industry data suggests..." without a named source
- Any statistic or percentage without a current-session web search citation

### Research Mode Safeguard

If `CONTENT_BRIEF.research_mode = true`:
- Treat all non-user facts as Intelligent Extension Zone content.
- Apply hallucination safeguards to every such claim.

---

## CTA Behaviour by content_goal

| content_goal | CTA Behaviour |
|---|---|
| educate | Soft learning CTA. Suggest related content or next step. No product push. |
| build-authority | Newsletter or follow CTA. Brand mention soft. |
| convert | Product or lead CTA. Hard CTA permitted. |
| persuade | Soft-to-hard depending on subtype. Opinion uses light invite only. |
| inform | Soft or none. Information delivery is the primary action. |
| entertain | No CTA or very soft brand mention only. |

CTA placement is subtype-specific. Refer to each subtype's component library for CTA tier and placement rules.

---

## G.1 — Humanise Pass Assignment

Apply the correct HUMANISE_PASS value for the subtype. Attach it as metadata to the output for processing by `skills/humanise/SKILL.md`. Do not apply Full Pass standards to Restricted or Quote-Protected subtypes.

| Subtype | Humanise Pass | Key Restriction |
|---|---|---|
| How-to / Tutorial | Full Pass | None |
| Listicle | Full Pass | None |
| Opinion / Thought Leadership | Full Pass | None |
| News & Updates | Full / Restricted | Opinion injection banned in Zone 1 factual reporting sections |
| Product Review | Full Pass | None |
| Comparison Post | Full Pass | None |
| Case Study | Restricted Pass | No opinion injection. No tone softening. Analytical register preserved. |
| Interview | Quote-Protected Pass | Framing prose only. Quoted material never rewritten, shortened, or smoothed. |
| Guest Post | Full Pass | None |
| SEO-Focused Article | Restricted Pass | SEO structure preserved. Humanisation on sentence level only. |
| Beginner's Guide | Full Pass | None |
| Ultimate Guide / Pillar Post | Full Pass | None |

**Full Pass standards:**
- Passive voice: under 10% of sentences
- Readability: Flesch score 60–70 for general audience, 50–60 for professional/sector content
- Paragraphs: maximum 4 sentences for digital content
- Opening line: must not start with 'In', 'The', 'Are you', or the brand name
- Every major section must contain at least one opinion, observation, or specific insight
- Sentence rhythm must vary — no 3+ consecutive sentences of the same length

**Restricted Pass — what applies:**
- Passive voice check
- Paragraph length check
- Basic rhythm variation
- Opening line check

**Restricted Pass — what does NOT apply:**
- Opinion injection — banned. Analytical register preserved.
- Tone softening — banned. Professional register preserved.
- Narrative rewriting — banned. Structure and sequence not altered.

**Quote-Protected Pass:**
- All Full Pass standards apply to framing prose only.
- Quoted material is never touched — not shortened, not rewritten, not smoothed, not rhythm-adjusted.
- The interviewee's speech patterns — fragments, directness, informality — are preserved exactly.

---

## G.2 — SEO Standards

**SEO Activation Rule.** The SEO layer activates only when:
- `CONTENT_BRIEF.keyword` exists (non-empty)
- AND `CONTENT_BRIEF.sub_type` is not one of: `opinion`, `news-updates`, `interview`, `guest-post`

Do not activate SEO structure based on pipeline assumptions about whether Phase 4 fired.

- Keyword in H1, first 100 words, at least 2 H2s, meta title, meta description
- Meta title: under 60 characters. Formula: Primary Keyword + Benefit/Angle + Brand
- Meta description: under 155 characters, includes action verb. Formula: Action verb + keyword + value prop
- LSI keywords: 5 per piece, woven naturally — never forced
- Internal link placeholders: minimum 2 per blog post
- Schema markup assessment: output as notes at end of content

---

## G.3 — Banned Phrases

**Category 1 — Generic AI-isms:** In today's digital/fast-paced landscape, In the modern era, In the ever-evolving world of, It's important to remember that, It's important to note that, Unlock the secrets, Unleash your potential, Harness the power of, A testament to, Dive deep into, Delve into, At its core, At the end of the day, Comprehensive guide, Think of it as

**Category 2 — Forbidden Transitions:** Furthermore, Moreover, In addition, Consequently, Additionally, Subsequently, Accordingly, Indeed, Not only...but also, In conclusion, To summarise, It goes without saying, Last but not least

**Category 3 — Lazy Adjectives:** Revolutionary, Game-changing, Cutting-edge, Robust, Seamless, Synergy, Pivotal, Transformative, Innovative, Groundbreaking, Dynamic, Powerful, Vibrant, Crucial role, Ever-evolving, Nuanced approach

**Category 4 — Corporate Verbs:** Leverage (use: use), Facilitate (use: help), Utilize (use: use), Endeavour, Underscore (use: show/prove), Optimize (use: improve), Revolutionize, Empower, Foster, Navigate (metaphorical), Circle back, Deep dive, Move the needle, Streamline

---

## Pre-Output Validation

Scan generated content for placeholder markers before producing final output.

If a placeholder remains:
- Notify the user that the section contains an unresolved placeholder.
- Do not output that section as final content.

---

## Output Format

Respect CONTENT_BRIEF.output_format:

| Value | Behaviour |
|---|---|
| markdown | Default. Structured markdown with headers, numbered lists, paragraphs. |
| html | Semantic HTML. H1/H2/H3 hierarchy. No inline styles. |
| plain-text | No markdown syntax. Headers as plain capitalised lines. |
| cms-ready | Markdown with CMS-friendly heading structure and placeholder image labels. |

After the main content, output as a separate block:
- Meta title
- Meta description
- Schema markup notes (when SEO layer fired)
- Internal link placeholder list

---

## Subtype Generation Rules

**Valid blog subtypes.** Normalize `CONTENT_BRIEF.sub_type` to lowercase before comparison. Then validate against the whitelist below. If the normalised value does not match one of these values → Hard Stop before generation:

- `how-to`
- `listicle`
- `opinion`
- `news-updates`
- `product-review`
- `comparison-post`
- `case-study`
- `interview`
- `guest-post`
- `seo-focused-article`
- `beginners-guide`
- `ultimate-guide`

**Subtype execution guard.** Only the rules belonging to the selected subtype are active. All other subtype rule sections must be ignored.

Example: if `sub_type = "case-study"` → only F.7 rules apply.

---

### F.1 — How-to / Tutorial

**Core Rule:** A humanised How-to transfers competence, not just information. The reader finishes feeling 'I can actually do this' — not just 'I now know the steps.'

**Humanise Pass:** Full Pass

**Subtype Signals:** step-by-step, how do I, guide to, tutorial, walk me through, how to set up / build / create / fix

**Skill Level Routing** (from CONTENT_BRIEF.reader_knowledge_level):

| Skill Level | Behaviour |
|---|---|
| Beginner | Prerequisites mandatory. 'Why' at every step. Checkpoint language at every critical step. Troubleshooting at failure points. Jargon defined on first use. |
| Intermediate | Prerequisites brief or linked. 'Why' at counterintuitive steps only. Checkpoints at key transitions only. Baseline knowledge assumed. |
| Advanced | No prerequisites. 'Why' only where non-obvious. No checkpoint language. Peer-level tone. Technical depth over breadth. |

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Prerequisites Block | Core | Structural | Always fires for Beginner. Optional for Intermediate and Advanced. | None |
| Tools / Materials List | Supporting | Structural | Fires for physical process how-tos. Optional for software/conceptual. | None |
| Time / Effort Estimate | Supporting | Narrative | Fires at all lengths. High value for reader self-selection. | None |
| Checkpoint Callouts | Conditional Core | Structural | Mandatory for Beginner. Supporting for Intermediate. Absent for Advanced. | None |
| Embedded Troubleshooting | Supporting | Narrative | Fires above 1,200 words. At the step where failure typically occurs — not at the bottom. | None |
| Variations Section | Enrichment | Narrative | Fires above 1,500 words only if brief contains that knowledge. Never fabricated. | None |
| Community Relevance Block | Supporting | Narrative | Fires when topic affects specific identifiable community. Web search fills. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. | None |
| Related Tutorials Link Block | Enrichment | Structural | Eligible at all lengths. Internal link placeholders only. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 1,200 – 2,000 words |
| Floor | 900 words |
| Ceiling | 2,500 words |
| Open slot trigger | Above 2,000 words — ask what specific sections to add |

**Writing Rules:**
1. Open with the reader's specific end state — not the tutorial's subject matter.
2. Steps must be numbered. Order is the point. Bullets are wrong for steps.
3. One action per step. 'Install X and configure Y' is two steps.
4. Steps must be concrete enough to execute.
5. Every step carries one 'why' sentence — embedded in the step, not in a theory section.
6. After every step that produces a visible result, include one checkpoint line.
7. At the 2–3 steps most likely to fail, embed the most common failure and its fix in one sentence.
8. Sentence structure alternates: action sentence (short, imperative) then context sentence (medium, explanatory).
9. Intro must be short — 2–3 paragraphs maximum before hitting prerequisites or step one.
10. Close with orientation forward and the natural next step. Never with a summary.

**Common Failure Modes:**
- Steps that describe intent instead of action
- Prerequisites buried in step 3 after the reader has already started
- 'Why' stripped out, leaving steps that feel arbitrary
- Vague outcome statement that protects the piece from accountability
- Overlong intro that front-loads theory before the reader gets to do anything
- Closing that recaps steps instead of confirming completion and orienting forward

---

### F.2 — Listicle

**Core Rule:** A listicle showcases — it does not compare. Each item is presented on its own merits. The moment items start competing against each other, it stops being a listicle and becomes a Comparison Post.

**Comparison Flag Rule (locked):** If the user's angle is 'best X' and items are competing products — ask: 'Are you looking to showcase each of these independently, or help the reader decide between them?' Second answer routes to F.6.

**Humanise Pass:** Full Pass

**The Four Listicle Types:**

| Type | Internal Logic |
|---|---|
| Best / Top | Curated ranking with editorial opinion behind each item. Strongest item last for impact OR first for CTR — pick one and maintain throughout. |
| Mistakes to Avoid | Problem-first structure. Each item is a failure mode. Taps into loss aversion. |
| Most Underrated / Hidden Gems | Contrarian structure. Hook is 'you haven't heard of these.' Credibility matters most here. |
| Comparison-style Listicle | Items assessed against consistent criteria. Consistency across items is the quality signal. |

**Item Substance Floor — mandatory for every item:**
- A specific descriptive subheading — not 'Notion' but 'Notion — best for teams that think visually'
- Two to four sentences of substance — what it is, who it is for, one specific detail that makes it memorable
- One opinion or editorial observation — the writer's point of view on this item. Description alone is not enough.

Test: can any item be removed without the list noticing? If yes, that item should not be there.

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Table of Contents | Core | Structural | Fires automatically above 1,200 words. Claude generates — no user input needed. | None |
| Categorised Sub-grouping | Supporting | Structural | Fires when list has 8+ items AND items naturally segment into 2–4 distinct groups. Never at the beginning. | None |
| Budget Tier Layer | Supporting | Narrative | Fires when items have pricing context. Own-product: ask user before applying. Third-party: web search verifies. Unavailable pricing → drop. | None |
| Expert Quote / Roundup | Enrichment | Narrative | Fires for third-party listicles above 1,500 words. Never fabricated. **[F.0.3]** | None |
| FAQ Section | Supporting | Structural | Third-party: web search fills. Own-product: soft single ask only, drop if skipped. | None |
| Community Relevance Block | Supporting | Narrative | Fires when topic affects a specific community. Web search fills. Dropped if nothing found. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 800 – 1,500 words |
| Floor | 600 words |
| Ceiling | 2,500 words. Above this → becomes an Ultimate Guide |
| Item count floor | Minimum 5 items |
| Item count ceiling | No hard ceiling. Above 20 items → TOC and Categorised Sub-grouping become mandatory |
| Open slot trigger | Above 1,500 words — ask what additional depth is wanted |

**Writing Rules:**
1. Number the items. Always. Bullets remove the sense of curation and editorial intent.
2. Subheadings must be specific and descriptive — scanners must get value from subheadings alone.
3. Every item needs one opinion or editorial observation.
4. Order logic is declared before writing item one and never changes mid-list.
5. The intro justifies why this list exists. Two to three paragraphs maximum.
6. The close gives the reader somewhere to go — never a summary of listed items.
7. The number in the headline matches the number of items exactly. No padding.
8. Consistent item length — if items vary significantly in depth, there must be a logical reason.

---

### F.3 — Opinion / Thought Leadership

**Core Rule:** Real, specific, defensible stance. Not manufactured. Not hedged. The stance is named in the first paragraph — not buried. The biggest failure mode: sounds opinionated but hedges every claim into meaninglessness.

**Humanise Pass:** Full Pass

**Stance is a Hard Stop.** If CONTENT_BRIEF.angle is absent and the user's stance is not in the brief — the piece cannot proceed. The user's opinion cannot be fabricated.

**Three Types:**

| Type | Structure | Core Requirement |
|---|---|---|
| Counter-narrative | Name the norm → dismantle it → offer alternative | Must engage with the strongest version of the opposing view. |
| Experience-backed | Story → what it revealed → why it matters | Fresh angle from personal or company experience. Story block is mandatory. |
| Industry analysis | What's happening → why it matters → what it means for reader | Forward-looking commentary. Industry Context Block is mandatory. |

**Sector Overlays:**
- Legal/CA/Finance: auto-add 'This is a personal/professional perspective, not legal/financial advice' framing.
- IT/SaaS: audience depth calibration — engineers vs product people vs C-suite.
- Healthcare: evidence-type routing — clinical evidence vs published research vs professional experience.
- Marketing/Agency: clarify whether challenging industry norm, common tactic, or competitor positioning.

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Stance + Opening Paragraph | Core | Structural | Always fires. Must simultaneously signal intelligence through vocabulary, state the claim (not tease it), and create one reason to stay. | None |
| Counter-argument Block | Conditional Core | Narrative | Mandatory for counter-narrative type. Supporting for experience-backed and industry analysis. Engages genuinely with the strongest opposing view — not hedging. | None |
| Evidence / Data Anchor | Core | Narrative | Always fires. Claude web-searches for supporting data if user doesn't provide it. Flagged in orientation statement when web-sourced. **[F.0.3]** | None |
| Credibility Anchor Block | Supporting | Narrative | Fires when user provides a credibility signal (personal experience, role, credentials). Placement: 2nd or 3rd paragraph after stance. Never fabricated. | None |
| Context Bridge | Supporting | Structural | Fires when topic is niche or technical with a mixed-familiarity audience. 1–3 sentences only. | None |
| Experience Story Block | Conditional Core | Narrative | Mandatory for experience-backed type. Absent for others unless user provides a story. | None |
| Industry Context Block | Conditional Core | Narrative | Mandatory for industry analysis type. Absent for others. **[F.0.3]** | None |
| Community Relevance Block | Supporting | Narrative | Fires when opinion topic affects a specific identifiable community. **[F.0.3]** | None |
| CTA (light only) | Supporting | Structural | Fires when brand has a relevant next step. No hard sell — invite response, suggest related content, soft brand mention only. | None |
| Author Bio | Supporting | Narrative | Strongly recommended for all Opinion pieces. Credibility matters more here than any other subtype. User must provide. | None |

**Boldness Rule (locked):** Write with full conviction of the stated stance. No hedging. No softening. No unsolicited balance. When stance is strongly critical of a real subject → run a web search sweep for documented public criticism, verified events, and historical record. Build the argument from verified facts only. If web search finds no credible evidence for a claim → that claim does not go in.

**Opening Paragraph Rule (locked):** The first paragraph must do three things simultaneously: (1) signal intelligence through vocabulary. (2) State the claim — not tease it. (3) Create one reason to stay. All three. First paragraph only.

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 700 – 1,200 words |
| Floor | 600 words |
| Ceiling | 1,800 words. Above this → flag and offer reframe |
| Open slot trigger | Above 1,200 words — ask what additional depth is needed |

**Common Failure Modes:**
- Hedging the claim in the body after stating it boldly in the opener
- Counter-argument block that is strawmanned — weakened version of the opposing view instead of the strongest version
- Evidence that is vague ('studies show', 'many experts believe') instead of specific and attributed
- Credibility anchor fabricated
- Conclusion that retreats to balance instead of landing the stance

---

### F.4 — News & Updates

**Core Rule:** Covers a real development in full — what happened AND what it means. Both zones mandatory. Reporting only = press release. Analysis only = leaves uninformed readers lost.

**Humanise Pass:** Full Pass on opinion/analysis sections. Restricted Pass on factual reporting sections — no opinion injection in Zone 1 factual content.

**Three Types:**

| Type | Emphasis | Key Rule |
|---|---|---|
| Breaking news take | Heavier on Zone 1 (confirmed). Speed matters. | Run web search immediately after user names the event to verify current status. |
| Trend analysis | Heavier on Zone 2 (intelligent extension). Longer shelf life. | Timeliness ceiling: if event is 4+ weeks old → flag and offer reframe as trend analysis. |
| Company / product update | 100% confirmed zone. Lead with reader benefit not brand milestone. | Intake is more demanding — Claude extracts more from user upfront since web search cannot fill product details. |

**Confirmed Zone / Intelligent Extension Zone for News:**
- Zone 1 (Confirmed): What happened, when, who, why significant. Complete account. From user + web search verification of public facts.
- Zone 2 (Intelligent Extension): Brand analysis, implications, expert reactions, what it means for reader. Built from publicly available verified information.
- Company/product update exception: Zone 1 is almost entirely user-provided. Zone 2 adds industry context, competitor landscape, and demand signals via web search.

**Emotion Rule (locked):** Emotion in the opening sequence is inferred from the nature of the news event. Urgency for breaking developments. Concern for negative impacts. Excitement for positive announcements. Outrage for injustice or failure. No question asked to the user. Claude's judgment call based on what the event actually is.

**Controversial Sourcing Rule (locked):** When web search returns conflicting information from credible sources on both sides — Claude does not pick a side silently and does not produce wishy-washy both-sides content. Write the controversial element directly and attribute it correctly. The controversy is attributed to named credible sources — not to the brand and not to Claude. Only named credible sources. No vague attributions like 'some experts say' or 'many believe.' If a claim cannot be attributed to a specific credible source, it does not go in.

**Sector Overlays:**
- IT/SaaS: product launch / platform change / industry regulation / emerging tech trend
- Legal/CA/Finance: regulatory change / court ruling / policy update / market event → auto-add disclaimer framing
- Healthcare: clinical development / policy change / research finding / public health issue
- Real Estate: market trend / policy change / local development + city/region specification

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Aggressive Headline (3 options) | Core | Structural | Always fires. Three variants: urgency, stakes, contrarian. User picks one. | None |
| Three-Part Opening Sequence | Core | Structural | Always fires. Part 1: curiosity builder (2–4 sentences, no reveal, emotionally consistent). Part 2: the reveal. Part 3: body opens. Emotion inferred from the event. | None |
| Context Block | Core | Structural | Always fires. What happened, when, who, why significant. Complete account before analysis begins. | None |
| Implication Layer | Core | Structural | Always fires — this is the 'so what' for the reader. Claude web-searches for documented implications if user cannot provide. Mandatory. | None |
| Brand Angle Block | Conditional Core | Narrative | Mandatory when content intent is opinionated commentary. Drops to Supporting for neutral reporting. Comes entirely from user input — confirmed zone. | None |
| Timeline Snapshot | Supporting | Structural | Fires when news event involves multiple sequential developments across more than one day OR multiple parties responding to each other over time. Maximum 6 date-stamped entries. Factual only. | None |
| Authority Quote Block | Supporting | Narrative | Fires when topic involves expert analysis, statistics are mentioned, or topic is controversial or high-stakes. Web search fills. Named credible sources only. Drop if nothing credible found. **[F.0.3]** | If substantive, Perspectives Block becomes optional. |
| Perspectives Block | Supporting | Narrative | Fires when multiple credible voices have publicly responded to the event. Three variants: public sentiment, expert opinion, brand take. Controversial sourcing rule applies. **[F.0.3]** | Optional if Authority Quote Block already covers external viewpoints sufficiently. |
| What Happens Next Block | Supporting | Narrative | Fires when story is ongoing — announced plans, scheduled developments, or anticipated responses exist. Web search fills. Speculation labelled 'expected' or 'anticipated' — never stated as confirmed. **[F.0.3]** | None |
| Origin-to-Present Context Block | Supporting | Narrative | Fires when news event involves a complex or niche subject the average reader may not have background on. 150–300 words. Mandatory for niche topics with mixed audiences. | If Context Block already covers sufficient background — reduce to a brief paragraph rather than a full block. |
| Community Relevance Block | Enrichment | Narrative | Fires when topic affects a specific identifiable audience or industry group. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. User must provide. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 600 – 1,000 words |
| Floor | 400 words |
| Ceiling | 1,500 words. Above this → flag and offer reframe |
| Timeliness ceiling | 4 weeks. Events older than 4 weeks → flag and offer reframe as trend analysis |
| Max structural depth | 6 major sections maximum |

**Common Failure Modes:**
- Opening that reveals the news in the curiosity builder — destroys the three-part opening structure
- Implication layer missing — the piece becomes just a summary, not an analysis
- Web search not run before generating — factual errors in news content damage brand credibility publicly
- Stale event treated as breaking news without flagging
- Controversial sourcing handled with vague attributions instead of named sources

---

### F.5 — Product Review

**Core Rule:** A detailed evaluation of a specific product or service written to help a reader make an informed purchase decision. The review's job: give them the honest, specific, experience-backed assessment they cannot get from the product's own website.

**Humanise Pass:** Full Pass

**Two Scenarios — routing from brief:**

**Scenario A — Third-Party Product Review:**
- Product is publicly known. Web search fills gaps.
- User provides: verdict direction and personal experience if any.
- Claude researches: product specs, pros/cons context, user testimonials, pricing, alternatives.

**Scenario B — Own Product Review:**
- Product is known only to the user. Web search is largely useless for product substance.
- User is the only source of truth.
- Mandatory Knowledge Framework extracted during intake: (1) core product identity, (2) product specifics, (3) competitive positioning, (4) honest assessment, (5) social proof.

**Pros / Cons Three-Tier Classification (locked — applies to all cons):**

| Con Tier | Definition | Action |
|---|---|---|
| Critical cons | Flaws that would genuinely make a specific type of reader choose a different product. They change the purchase decision. | Always included. Named specifically. Minimum 1 mandatory. |
| Contextual cons | Limitations that are industry-standard, or better-than-average when stated correctly with context. | Reframed as qualified pros. |
| Negligible cons | Flaws so minor or so universal to the category that mentioning them adds no value. | Dropped entirely. |

**Hard Rule:** A review with zero critical cons is not credible. If the product genuinely has no critical cons, state that explicitly with a specific justification — never just omit the cons section.

**Own-Product Writing Rules:**
- The cons section is the credibility engine — not an obligation. A brand that honestly names its product's real limitations builds more trust than ten positive claims.
- The 'Who It's Not For' section is more critical here than in any other review type.
- **Verdict Misalignment Rule:** If the assembled content materially contradicts the stated verdict direction — flag this before generating. User decides. Never generate content where the body and verdict tell different stories.

**Intro Rule:** Two to three sentences maximum before the Quick Take Summary Box. Credentials line + what was tested + step aside. No brand storytelling, no industry context, no warm-up.

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Quick Take Summary Box | Core | Structural | Always fires. Placed at the top before the intro. Contains: product name, one-line verdict, who it's best for, rating, where to buy. | None |
| Reviewer Credibility Block | Core | Narrative | Always fires. First or second paragraph. Why this person or brand has the right to review this product. User must provide — cannot be fabricated. If skipped: write intro without credibility signal and flag it. | None |
| Product Overview | Core | Structural | Always fires. What it is, what it does, who makes it, key specs or features in plain language. Web search fills for publicly known products. User must provide for own/niche products. | None |
| Pros Block | Core | Structural | Always fires. Minimum 3 pros. Each one specific and evidence-backed — no generic selling points. | None |
| Cons Block | Core | Structural | Always fires. Three-tier con classification applies. Minimum 1 critical con mandatory for credibility. | None |
| Who It's For / Who It's Not For | Core | Narrative | Always fires. Both sides mandatory. 'Not for' section is as important as 'for' section — disarms reader defensiveness and builds trust. | None |
| Verdict and Recommendation Block | Core | Structural | Always fires — placed at the close. Must match the verdict direction set in Phase 3. Verdict misalignment rule applies. | None |
| In-Depth Experience / Testing Section | Conditional Core | Narrative | Mandatory when user has personal experience with the product. Drops to Supporting when review is research-based only. Cannot be fabricated. | If user has no personal experience → drops to Supporting. Web-searched user testimonials fill the gap instead. |
| Pricing Breakdown | Supporting | Structural | Fires when product has pricing tiers, subscription models, or non-obvious pricing structure. Web search verifies current pricing. Never fabricated. **[F.0.3]** | None |
| Alternatives Block | Supporting | Narrative | Fires when verdict is mixed or critical, or when product clearly suits only a segment. Maximum 3 alternatives. Web search fills. Drop if user's goal is purely promotional. | None |
| User Testimonials / Social Proof | Supporting | Narrative | Fires when review is research-based rather than hands-on, or when length justifies. Web search: verified public sources only (G2, Trustpilot, Reddit). Attributed to source. **[F.0.3]** | None |
| FAQ Section | Supporting | Structural | Fires when product has common objections or frequently searched questions. Claude uses People Also Ask for the product keyword. | None |
| Community Relevance Block | Enrichment | Narrative | Fires when product serves a specific identifiable community. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 900 – 1,500 words |
| Floor | 700 words |
| Ceiling | 2,500 words |
| Open slot trigger | Above 1,500 words — ask what additional depth is needed |

**Common Failure Modes:**
- Review with no negatives — destroys credibility instantly
- Fabricated personal experience — the most damaging failure for brand trust
- Verdict that contradicts the pros/cons — confused reader, zero conversion
- No 'Who It's Not For' section — feels like a sales pitch not a review
- Pricing information outdated or incorrect — kills purchase intent
- Alternatives that include direct competitors the user would not want mentioned
- Own-product review where Claude guesses product details not provided by the user

---

### F.6 — Comparison Post

**Core Rule:** Puts two or more products, services, or approaches head-to-head and helps the reader decide which is right for them specifically. The comparison's job is to unstick them by showing which option wins for which type of person under which conditions.

**Humanise Pass:** Full Pass

**Three Variants:**

| Variant | Description | Key Distinction |
|---|---|---|
| Head-to-head (classic) | Two specific options compared directly. Most purchase-ready intent. | Equal depth on both options across the same criteria. |
| Alternatives post | One known option vs a field of alternatives. Reader is considering leaving a product or hasn't committed yet. | The 'baseline' option is the anchor. Alternatives are evaluated relative to it. |
| Approach comparison | Not products but methods, strategies, or philosophies. Lower purchase intent, higher educational value. | No spec table applies. Criteria are conceptual. Use Case Segmentation is especially important here. |

**The Bias Problem:** Comparison Post is the only subtype where the reader actively expects objectivity. Brand bias is the single biggest credibility killer. If the user's own product is one of the items being compared — this must be disclosed. Not buried. Not softened. Stated clearly near the top. A comparison where the user's product wins everything is not trusted. Wins must be earned through specific criteria evidence, not declared.

**The Decision Framework — Core Structural Rule:** A Comparison Post without a clear 'who should choose what' conclusion has failed its primary job. The conclusion must segment the reader and direct each segment clearly with specific reasoning. 'It depends' is not an acceptable conclusion.

**Criteria Section Writing Rules:**
- Minimum 4 criteria. Maximum 8 criteria.
- Criteria are selected based on what the target reader actually cares about — not what the brand wants to highlight.
- Each criterion section: H2 subheading → how Option A performs → how Option B performs → which wins for which reader type.
- The user's own product must earn its wins through evidence. If it loses on certain criteria — those losses are acknowledged honestly.

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Headline | Core | Structural | Always fires. Must contain 'vs' or 'or' or 'alternatives' to signal comparison intent. Two options generated: SEO-direct and curiosity-led. | None |
| Decision Framing Introduction | Core | Structural | Always fires. States what is being compared, who it is for, and what criteria will be used. No warm-up. Two to three sentences maximum. Bias disclosure lives here if applicable. | None |
| Quick Comparison Summary Box | Core | Structural | Always fires. Placed immediately after introduction. Contains: options being compared, best for each, verdict per use case, rating if applicable. | None |
| Individual Option Overviews | Core | Structural | Always fires. One brief section per option. Enough context for an unfamiliar reader. Web search fills for publicly known products. User provides for own products. | None |
| Criteria-Based Comparison Sections | Core | Structural | Always fires. This is the body. Each section compares all options on one criterion. Minimum 4 criteria. Maximum 8 criteria. Each section: criterion name → how Option A performs → how Option B performs → which wins for which reader type. | None |
| Use Case Segmentation Block | Core | Narrative | Always fires — this is the conclusion. Segments the reader by profile and directs each segment to the right option with specific reasoning. Minimum two distinct reader profiles. No 'it depends' conclusions. | None |
| Comparison Table | Conditional Core | Structural | Mandatory when comparing products with multiple comparable specifications. Supporting for approach comparisons where specs do not apply. | None |
| Bias Disclosure Block | Conditional Core | Structural | Mandatory when user's own product is one of the options being compared. Placed in the introduction. One to two sentences. Frames disclosure positively. | None |
| Pricing Comparison | Supporting | Structural | Fires when options have different pricing structures or when price is a key decision driver. Web search verifies current pricing. **[F.0.3]** | None |
| User Sentiment Block | Supporting | Narrative | Fires when options are publicly known and have verifiable user reviews. Web search: Reddit, G2, Capterra, Trustpilot. Attributed to source. **[F.0.3]** | None |
| FAQ Section | Supporting | Structural | Fires when People Also Ask results exist for the comparison keyword. Claude uses web search to identify real questions. | None |
| Community Relevance Block | Enrichment | Narrative | Fires when comparison topic affects a specific identifiable community. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 1,000 – 2,000 words |
| Floor | 800 words |
| Ceiling | 2,500 words |
| Criteria cap | 8 maximum. Flag if user requests more and recommend combining or cutting. |
| Open slot trigger | Above 2,000 words — ask what additional depth is needed |

**Common Failure Modes:**
- User's own product wins every single criterion without evidence — destroys credibility
- No bias disclosure when user's product is included — reader detects it and leaves
- Criteria chosen based on what the brand wants to highlight rather than what the reader cares about
- Vague conclusion — 'both are great options depending on your needs' — reader leaves with no decision made
- Pricing information outdated
- Too many criteria — above 8 the piece becomes exhausting

---

### F.7 — Case Study

**Core Rule:** A documented proof of transformation. It shows a real challenge, what was done to solve it, and what measurably changed as a result. The reader's question is not 'what do you do' — it is 'can you do it for someone like me.'

**Humanise Pass:** Restricted Pass — no emotional amplification, no persuasive language, no urgency language. Analytical register preserved throughout.

**Tone Architecture (non-negotiable, locked):** Professional, factual, and explanatory. Not narrative. Not emotional. Not persuasive. Explicitly banned: emotional amplification, attention-seeking opening lines, persuasive language, urgency language, scarcity, fear-based framing, superlatives unless directly supported by a specific metric in the brief. Write as if presenting to a board or peer review panel.

**Two Variants:**

| Variant | Description | Tone Difference |
|---|---|---|
| Client / Customer Case Study | User's brand helped a client achieve a result. Client is the hero. Brand is the enabler. | Third-person for client. More external-facing. Anonymisation rules apply. |
| Self Case Study | User's brand achieved a result for itself — a campaign, a process improvement, a product launch. | First-person or brand voice. More internal credibility focus. No anonymisation needed. |

**Confirmed Zone — Own-Subject Rule (locked):** Every critical element — the challenge, the strategy, the implementation, the results — comes entirely from the user. Web search is permitted ONLY for: industry benchmarks (to frame results in context), competitor landscape (if relevant to the objectives section), definition of technical terms, general background context for niche industries.

If Claude finds itself about to write a detail that was not provided by the user — STOP. Ask for it or flag it as missing. Never approximate. Never infer specifics from context.

**Anonymisation Rules:**

| Sector | Rule |
|---|---|
| Legal | Client details anonymised by default unless user explicitly confirms permission to name. |
| Healthcare | Patient-adjacent cases always anonymised. Institutional cases can be named if user confirms. |
| Finance | Outcome metrics shareable. Client identity requires explicit user permission. |
| HR | Role titles preserved. Company names anonymised unless user confirms. |
| All other sectors | Claude asks once: 'Can we name the client or should we keep it anonymised?' |

**Metrics Rule:** Results must be specific. Approximate is acceptable — 'roughly 40% increase' is honest and usable. Vague is not — 'significant improvement' means nothing. If user cannot provide metrics after one nudge — flag in orientation statement: 'This case study will read more as a testimonial than a data-backed proof piece without metrics. That is still valuable — just want you to know the difference before we proceed.'

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Headline with Result | Core | Structural | Always fires. Lead with the outcome metric — not the client name and not the service. Format: 'How [Subject] Achieved [X Result] in [Timeframe]'. | None |
| Quick Summary Box | Core | Structural | Always fires. Placed at top before any body content. Contains: subject snapshot, problem in one line, solution in one line, key result metric. | None |
| Problem Statement | Core | Structural | Always fires. One to two sentences at the top of the piece. Formally states the problem before the narrative begins. Distinct from the Challenge Detail. | None |
| Strategy | Core | Narrative | Always fires. What was decided before implementation — the thinking, the approach chosen, the reason this route was taken over others. Strategic reasoning, not action steps. | None |
| Implementation | Core | Structural | Always fires. Step-by-step account of what was actually executed — how it happened, in what sequence, what tools or processes were used. Separated from Strategy deliberately. | None |
| Measurable Results | Core | Structural | Always fires. Specific metrics. Before vs after framing where available. Approximate numbers acceptable. Vague descriptors ('significant', 'dramatic') never acceptable. | None |
| Closing | Core | Structural | Always fires. One to two sentences. Orients the reader toward their own situation. Never recaps what was just covered. Invites the next action without being promotional. | None |
| Before vs After Comparison | Conditional Core | Structural | Mandatory when user has clear before-state metrics. Drops to Supporting when only after-metrics are available. | None |
| Client Quote Block | Conditional Core | Narrative | Mandatory for client case studies when the client has given permission and provided a quote. Supporting for self case studies. Never fabricated under any circumstance. **[F.0.3 applies — even though user-provided, must be verified as genuinely provided]** | None |
| Background Context | Supporting | Narrative | Fires when case involves a niche topic or when the reader may lack industry context. Web search fills general industry background. **[F.0.3 applies for web-sourced background]** | None |
| Objectives | Supporting | Structural | Fires when user can provide the measurable goals set before the work began. Different from results — objectives are the target, results are what happened. | None |
| User Personas | Supporting | Narrative | Fires when the work involved understanding a specific audience segment. User must provide. | None |
| Insights and Takeaways | Supporting | Narrative | Fires above 1,000 words. Key insights discovered during the process plus lessons the reader can apply. | None |
| Next Steps | Supporting | Narrative | Fires when user can provide continued client engagement details or a natural recommendation for the reader facing a similar challenge. | None |
| Process Walkthrough | Supporting | Structural | Fires when the solution benefits from being shown in sequential steps beyond the Implementation section. | None |
| Community Relevance Block | Enrichment | Narrative | Fires when the case study topic affects a specific identifiable industry community. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 800 – 1,500 words |
| Floor | 600 words |
| Ceiling | 2,000 words. Above this → becomes a report |
| Open slot trigger | Above 1,500 words — ask what additional depth is needed |

**Writing Rules:**
1. Lead the headline with the result — not the client name and not the service.
2. The Definition paragraph opens the body — calm, professional orientation of what this case covers and what the reader will understand by the end.
3. Problem Statement sits immediately after the Definition paragraph — one to two sentences, formally stated.
4. Strategy and Implementation are two separate sections — never collapsed. Strategy = the thinking. Implementation = the doing.
5. Results section uses before/after framing — not just the after number. 'Improved from X to Y' is more powerful than 'achieved Y'.
6. Client quotes go next to the result they corroborate — not grouped at the end.
7. The close orients the reader toward their own next step — never a summary.
8. Every specific detail, metric, and claim comes from the user. Claude never fills gaps with plausible-sounding fabricated numbers.

**Common Failure Modes:**
- Results section with no numbers — reads as testimonial not case study
- Strategy and Implementation collapsed — reader cannot see the thinking behind the work
- Emotional or persuasive language in any section — undermines professional credibility
- Client quotes fabricated — single most damaging failure mode for trust
- Metrics that are vague ('significant', 'dramatic', 'notable')
- Close that summarises instead of orienting forward
- Anonymised sectors where identifying client details accidentally appear

---

### F.8 — Interview

**Core Rule:** Every other blog subtype has one voice. The interview blog has two — and Claude is neither of them. Claude is the invisible editor: shaping the exchange into something a reader wants to read without making it feel shaped.

**Humanise Pass:** Quote-Protected Pass — full pass applies to framing prose only. Quoted material is never touched, not shortened, not rewritten, not smoothed, not rhythm-adjusted.

**Tone Architecture:** Tone is dictated by the interviewee's register, not the brand's. The interviewer's framing prose matches brand voice. The interviewee's answers preserve their natural speech patterns — fragments, directness, technical language, informality — within reason.

Hard prohibition: Claude never standardises the interviewee's voice into clean corporate prose.

Criteria for rambling vs character: if removing a sentence loses a specific opinion, fact, or personality signal — it stays. If removing it loses nothing except words — it goes.

**Three Format Types:**

| Format | When to Use |
|---|---|
| Q&A | Interviewee's voice dominates, minimal framing. Best when subject is known or exact words carry authority. |
| Narrative | Writer synthesises into flowing prose, quotes used as evidence. Best when subject needs introducing. |
| Hybrid (default) | Narrative framing with embedded Q&A blocks for highest-value exchanges. Default when user hasn't specified. |

**Source Material Paths:**
- **Path A — Transcript or notes exist.** Extract, edit, generate.
- **Path B — Interview not yet conducted, user is the interviewer.** Claude researches the guest via web search and builds a sequenced question set. Generation deferred until answers come back.
- **Path C — User is the interviewee providing their own answers.** Narrative dump model applies. Claude shapes the user's own voice.

Podcast transcript note: If source is a podcast transcript — Claude applies heavier editorial pass on filler, crosstalk, and incomplete exchanges before shaping the piece.

**Hard rule:** Claude never fabricates, smooths, or infers interview answers. If an answer is missing, Claude flags the gap and asks. No plausible-sounding substitution under any circumstance.

**Sector Overlay Behaviour:**

| Detected Angle | Component Activated | Behaviour Change |
|---|---|---|
| Founder story | Background / Origin Story | Activate component. Emphasise journey over credentials. |
| Technical deep-dive | Editor's Note / Context Insert | Activate component. Increase quote density. Define technical terms for general reader. |
| Product/market perspective | Pull Quote Highlight | Activate if strong product claim made. Framing prose stays factual. |
| Clinical professional | Disclaimer framing | Auto-add medical/legal disclaimer to relevant answers. |

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Subject Introduction | Core | Structural | Always fires. One paragraph. Who they are, why their opinion matters on this specific topic. Not a CV. | None |
| Framing Intro | Core | Structural | Always fires. What prompted the interview, what the reader gets from it. 2–3 sentences max. | None |
| Interview Body | Core | Structural | Always fires. The exchange in whichever format was routed. | None |
| Closing Reflection | Core | Structural | Always fires. Final quote selected for resonance. Writer steps back. Never a summary. | None |
| Quote Density Rule | Core | Narrative | Always enforced. Interviewee quotes must occupy at least 60% of the article in narrative and hybrid formats. | None |
| Pull Quote Highlight | Conditional Core | Structural | Fires when one answer contains a contrarian statement, strong opinion, memorable phrasing, or shareable insight strong enough to stand alone as a section anchor. | None |
| Background / Origin Story | Supporting | Narrative | Fires when interviewee's journey is part of what makes their perspective credible. Drops or is absorbed if Subject Introduction already covers origin. | None |
| Lessons / Takeaways Block | Supporting | Narrative | Fires above 1,200 words when interviewee explicitly states lessons clearly in substance. User must provide or Claude extracts only when stated clearly — never inferred. | None |
| Community Relevance Block | Supporting | Narrative | Fires when interview topic directly affects an identifiable industry community. Web search fills. **[F.0.3]** | None |
| Editor's Note / Context Insert | Enrichment | Narrative | Fires when part of the interviewee's answer references an event, term, or context the reader may not have. One sentence, bracketed or footnoted. | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 1,000 – 1,800 words |
| Floor | 800 words |
| Ceiling | 2,200 words. Exception: deeply technical expert interview where sustained depth is the point. |
| Open slot trigger | Above 1,800 words — ask if additional exchange material exists |
| Hard material constraint | Word count is source-constrained. Thin answers = short piece. That is the format being honest, not a failure. |

**Writing Rules:**
1. Subject introduction frames why this person's opinion matters on this specific topic — not their general biography. One paragraph. Before the first question or quote appears.
2. Framing intro states what prompted the interview and what the reader gets from it. Two to three sentences. Never throat-clearing.
3. In Q&A format, every question must earn its place. A question that produces a generic answer is an editorial failure, not an interviewee failure.
4. The interviewee's natural speech patterns are preserved. If removing a sentence loses a specific opinion, fact, or personality signal — it stays.
5. Disagreement, tension, and unexpected admissions are never edited out. These are the most valuable moments and the most shareable.
6. In narrative and hybrid format, transitions between writer framing and interviewee quotes must be invisible.
7. The close is built around the interviewee's most resonant final statement. Writer selects it and steps back. Never a writer's summary.

**Common Failure Modes:**
- Fabricated or paraphrased-beyond-recognition quotes — single most damaging failure for this format
- Throat-clearing intro that introduces the topic instead of the person
- Weak questions that produce generic answers
- Interviewee's voice sanitised into corporate prose
- Tension and disagreement edited out
- Close that summarises instead of landing the most resonant quote
- Piece generated before actual answers exist — question framework mistaken for source material

Note: Podcast blog is NOT a separate subtype. Routes to Interview. Handled as a source material variant within Path A.

---

### F.9 — Guest Post

**Scope:** Direction B only — the brand is the guest. This is a content creation task. Direction A (brand is the host, editorial task) is out of scope.

**Core Rule:** A guest post is an authority building exercise, not a content creation exercise. The brand is writing for an audience it does not own, on a platform it does not control, under a name that may or may not be its own. The piece succeeds when the host platform's audience walks away with a strong impression of the author's expertise — not when it drives direct conversions.

**Humanise Pass:** Full Pass

**Tone Architecture:** The voice belongs to the guest author — not the host platform. Claude never mirrors the host platform's tone. If the named author is a specific individual, their personal voice takes precedence over brand voice. If the author is the brand or anonymous, brand voice applies.

Hard prohibition: Claude never writes a guest post in the host platform's tone — that defeats the purpose of the format entirely.

**Guest Post Narrative Structure:**

| Stage | Content |
|---|---|
| 1. Hook | Unique angle revealed quickly. Curiosity-led for cold audiences. |
| 2. Context Bridge | Mandatory for cold audience. Fires automatically. Skipped for warm audience. |
| 3. Core Argument | The specific perspective only this author could make. |
| 4. Evidence / Examples | Data, stories, or cases supporting the argument. |
| 5. Insight or Framework | What the reader can take away and apply. |
| 6. Reader-Oriented Conclusion | The reader's next thought. Never a brand push. |

**CTA Rule — Two Slots Maximum:**

| Slot | Location | Rule |
|---|---|---|
| Slot 1 — Body contextual mention | Body only | Brand or product mentioned only if it directly supports the point being made. One mention maximum. Never forced. Dropped if promotional. Cannot coexist with a conclusion CTA. |
| Slot 2 — Author Bio CTA | Author bio only | Always fires unless anonymous. One link, one action. This is the only hard CTA slot. Never in the conclusion. |

Hard rule: No CTA in the conclusion. Conclusions on guest posts belong to the reader's next thought, not the brand's next offer.

**Sector Overlays:**
- IT/SaaS: Is this thought leadership, a technical tutorial, or a product perspective? Technical tutorial → Editor's Note activates.
- Legal/CA/Finance: Auto-add 'not legal/financial advice' where relevant.
- Healthcare: Clinical content → medical disclaimer fires automatically.
- Marketing/Agency: Results-first framing. Any client metrics referenced need 'results may vary' qualifier.
- Real Estate: Location specificity and regulatory mentions (RERA etc.) apply where relevant.

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Context Bridge | Conditional Core | Structural | Mandatory when cold audience is detected. Curiosity-led opening fires with it. Drops for warm audience. | None |
| Unique Angle Block | Core | Structural | Always fires. The argument only this author could make. | None |
| Author Introduction | Core | Structural | Always fires unless author is anonymous. One paragraph. Why they have the right to write about this topic. | Drops entirely when anonymous route is selected. |
| Body Argument | Core | Structural | Always fires. Built for host platform's audience throughout. | None |
| Author Bio CTA | Core | Structural | Always fires unless anonymous. One link, one action. | Drops when anonymous route is selected. |
| Body Contextual Mention | Conditional Core | Narrative | Fires only when brand directly supports the point being made. One mention maximum. Never forced. | Cannot coexist with conclusion CTA. |
| Lessons / Takeaways Block | Supporting | Narrative | Fires above 1,200 words when post explicitly offers transferable lessons. Must come from user-provided substance. | None |
| Data / Research Block | Supporting | Narrative | Fires when user provides stats or original data. Web search verifies third-party data. Brand's own internal data is always allowed. **[F.0.3 applies for third-party data]** | None |
| Community Relevance Block | Supporting | Narrative | Fires when topic affects an identifiable industry community. Web search fills. **[F.0.3]** | None |
| Editor's Note / Context Insert | Enrichment | Narrative | Fires when post references an event or term host platform's audience may not have. One sentence, bracketed. | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. Drops when anonymous route is selected. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 1,000 – 1,800 words |
| Floor | 800 words |
| Ceiling | 2,000 words. Above this — ask if host platform explicitly requested long-form. |
| Open slot trigger | Above 1,800 words — ask what additional depth the host platform has requested |
| Hard rule | No CTA in the conclusion |

**Writing Rules:**
1. The unique angle is stated in the first two paragraphs — not teased, not built toward.
2. The host platform's audience is the primary reader throughout. Every example and reference is calibrated to them — not to the guest's own existing audience.
3. Cold audience opening fires a curiosity-led hook before any context is established.
4. The author's voice takes precedence over brand voice when a named individual is the author.
5. Two CTA slots maximum. One contextual body mention only if it directly supports the point. One author bio CTA always. No CTA in the conclusion under any circumstance.
6. The conclusion belongs to the reader's next thought. Never a brand push.
7. When the author is anonymous, all personal voice signals, biographical references, and author attribution are removed from the entire piece — not just the bio section.

**Common Failure Modes:**
- Piece written for the guest's own audience instead of the host platform's audience
- No unique angle — generic content with a byline
- Aggressive CTA in the conclusion
- Author's personal voice smoothed into brand voice
- Context bridge missing for cold audience
- Anonymous route selected but personal voice signals left in the piece accidentally

---

### F.10 — SEO-Focused Article

**Core Rule:** This is the only blog subtype where structure is decided before content. F.10 starts with a SERP and builds the message inside what Google has already decided it wants to rank. The piece succeeds when it ranks AND reads like a human wrote it. Either condition alone is a failure.

**Humanise Pass:** Restricted Pass — SEO structure preserved. Humanisation on sentence level only. No structural alterations.

Hard prohibition: if Claude cannot write a required SEO section in a way that passes humanisation standards — flag it rather than publishing robotic content.

**SERP Analysis System — Five Decisions Locked:**

**Decision 1 — Intent Detection (silent):** Claude detects intent silently via web search. Transactional or navigational intent triggers an immediate hard stop flag: "The top results for this keyword are [product/brand pages] — an article won't rank here. This keyword needs a web page, not a blog post."

**Decision 2 — SERP Analysis Layer:**
- Word count floor — always mandatory. Sets depth floor before writing starts.
- SERP features present — always mandatory.
- PAA questions — mandatory only when they appear in the SERP.
- Content gaps — mandatory only above 1,400 words.
- Competitor content structure — always mandatory. Filter: prefer informational content formats (articles, guides, listicles, comparison posts). Exclude product pages, landing pages, homepages, e-commerce listings.

**Decision 3 — Keyword Cluster Strategy:** Filter top 10 results to blog/article pages only. Extract primary keywords each article is targeting. Prioritise semantic keywords appearing across 3 or more results. Single-result keywords dropped.

**Decision 4 — Rerouting Rule:** When SERP analysis reveals the correct structure is another subtype (e.g. listicle, comparison) — adapt the structure silently but note it in the orientation statement. Generate immediately.

**Decision 5 — Humanisation vs SEO Tension Rule:** SEO requirements govern structure. Humanisation standards govern writing. Both are non-negotiable.

**Three Skeletons:**

| Skeleton | Intent | Structure |
|---|---|---|
| Skeleton A | Informational | Hook → Definition/What is it → Why it matters → Core explanation sections → PAA sections → Conclusion with next step |
| Skeleton B | Commercial investigation | Hook → Quick answer featured snippet → Evaluation criteria → Options/breakdown sections → PAA sections → Recommendation → CTA |
| Skeleton C | Mixed or unclear | Hook → Definition → Core argument → Supporting sections → PAA sections → Conclusion |

**Featured Snippet Formatting Rule:** Format the snippet answer according to the snippet type observed in the SERP. Paragraph snippets: 40–60 words. List snippets: numbered or bulleted list format. Table snippets: table format. Always within the first 200 words.

**Topical Authority Coverage Rule:** Ensure major subtopics appearing across multiple ranking articles are addressed. This goes beyond keyword cluster — it covers the full topic scope that Google rewards for comprehensive coverage.

**Sector Overlays:**
- IT/SaaS: Technical depth calibrated to audience. Schema: Article or HowTo.
- Legal/CA/Finance: YMYL classification. Auto-add disclaimer. E-E-A-T signals critical. Author credentials mandatory.
- Healthcare: YMYL classification. Medical disclaimer mandatory. Author credentials mandatory. Anonymous author route not available.
- Real Estate: Location specificity mandatory.
- Marketing/Agency: Results-first. Any statistics referenced must be sourced.

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Featured Snippet Block | Conditional Core | Structural | Mandatory when SERP shows featured snippet. Format matches SERP snippet type. Within first 200 words. | None |
| PAA Sections | Conditional Core | Structural | Mandatory when PAA boxes appear in SERP. Each PAA question becomes H2 or H3. Answered directly in brand voice. | None |
| Keyword Cluster Coverage | Core | Structural | Always fires. Covers semantic keywords from top 10 blog results naturally. Prioritise keywords appearing in 3+ results. Never forced. | None |
| Content Gap Section | Conditional Core | Narrative | Fires above 1,400 words when SERP analysis reveals angles top results don't cover. Brand's unique expertise fills this. | None |
| Topical Authority Coverage | Conditional Core | Structural | Fires when SERP reveals subtopics appearing across multiple ranking articles. Ensures comprehensive topical coverage. | None |
| Author Credentials Block | Conditional Core | Narrative | Mandatory for YMYL sectors. Supporting for all others. User must provide. | None |
| Definition / What Is Block | Conditional Core | Structural | Mandatory for Skeleton A. Fires when keyword is a concept requiring definition. | None |
| Evaluation Criteria Block | Conditional Core | Structural | Mandatory for Skeleton B. Defines criteria before evaluation begins. | None |
| Data / Statistics Block | Supporting | Narrative | Fires when user provides original data or web search returns credible third-party stats. Named sources only. **[F.0.3]** | None |
| Expert Quote Block | Supporting | Narrative | Fires above 1,400 words. Only when credible named source clearly available from web search. Skip if source is unclear. **[F.0.3]** | None |
| Internal Link Block | Supporting | Structural | Always suggested. Minimum 2 placeholders. | None |
| FAQ Section | Supporting | Structural | Fires when PAA questions exist beyond those already covered as H2 sections. | If PAA sections already cover the questions — reduce to remaining unanswered only. |
| Community Relevance Block | Enrichment | Narrative | Fires when topic affects specific identifiable community. Web search fills. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Mandatory for YMYL sectors (escalates to Conditional Core). Otherwise fires when user provides it. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 1,200 – 2,500 words |
| Floor | Set by competitor depth — minimum of top 3 competitor average word count. Never below 900 words. |
| Ceiling | 3,000 words. Above this → flag and offer reframe as F.12. |
| Open slot trigger | Above 2,500 words — ask what additional depth the user wants |
| Hard rule | Word count is evidence-driven, not target-driven. |

**Writing Rules:**
1. Structure is decided by SERP analysis before writing begins.
2. Featured snippet answer appears within the first 200 words when SERP shows a featured snippet. Format matches snippet type.
3. Every PAA question identified in SERP analysis becomes a mandatory section. Answered directly and in brand voice.
4. Keyword cluster coverage is natural — never forced. If a keyword cannot be included naturally, it is dropped.
5. The brand's unique angle appears in at least one section that top-ranking competitors do not cover.
6. YMYL content requires author credentials to appear. Anonymous author route not available for YMYL sectors.
7. SEO requirements govern structure. Humanisation standards govern writing. Neither overrides the other.
8. Competitor structure informs the skeleton but does not dictate sentence-level writing. Structure is borrowed. Everything inside it is original.
9. Internal link placeholders placed at natural topic transitions — never forced.
10. When SERP analysis routes the piece to a different structure than the user expected — orientation statement notes it. Generation proceeds immediately.

**Common Failure Modes:**
- Article written without SERP analysis — structure guessed instead of evidence-based
- Featured snippet answer missing or placed too late
- PAA questions identified but not included as sections
- Keyword cluster forced mechanically — reads as keyword-stuffed
- Brand angle missing — article is a rewrite of existing results
- Word count set by preference not by competitor depth floor
- YMYL content generated without author credentials
- SEO structure used as excuse for generic writing
- Piece grows beyond 3,000 words without flagging

---

### F.11 — Beginner's Guide

**Core Rule:** A Beginner's Guide does one thing no other blog subtype does: it earns the reader's commitment before asking for their effort. Motivation, honest expectation setting, and acknowledged challenges come before a single instructional word. The guide is wide but shallow. It introduces and orients. It links to depth rather than containing it. The moment it starts going deep on multiple sub-topics it has crossed into Ultimate Guide territory.

**Humanise Pass:** Full Pass

**Tone Architecture:** Warm, honest, and encouraging — but never patronising. The tone assumes intelligence — it just assumes no prior knowledge.

Hard prohibition: Claude never writes down to the reader. Explaining something simply is not the same as treating the reader as incapable.

**Routing Boundaries:**

F.11 vs How-to: If the topic is broad enough to require foundational understanding before any steps make sense — ask the user which format they want.

F.11 vs F.12 scope ceiling: Above 2,000 words — flag: 'This is expanding beyond beginner territory — do you want to keep this as an introduction that links to deeper content, or build it out as a full Pillar Post?'

Hard scope rule: a Beginner's Guide links to depth. An Ultimate Guide IS the depth.

**Default Structural Flow:**

| Stage | Content |
|---|---|
| 1. Motivation | Why this topic matters to the specific reader. Fires before any instruction begins. |
| 2. Definition | What this topic actually is. Plain language. No assumed knowledge. |
| 3. Core Concepts | Foundational understanding built progressively. Each section assumes only what came before. |
| 4. Challenges | What's hard and why. Acknowledged before the reader hits it. |
| 5. First Steps | What the reader can do immediately to begin. |
| 6. Next Resources | Where to go for depth. Internal links primary, external resources secondary. |

**Sector Overlays:**
- IT/SaaS: Jargon defined on first use without exception. No assumed tech knowledge.
- Legal/CA/Finance: YMYL. Expectation setting must include honest scope limitation — 'foundational knowledge, not professional advice.' Auto-add disclaimer.
- Healthcare: Medical disclaimer mandatory. Challenges block must include 'when to seek professional guidance' signal.
- Education: Curriculum or board context where relevant. Age and role calibration active.
- Marketing/Agency: Expectation setting must be honest about timeline to results — no guaranteed outcome language.
- E-commerce: Soft CTA rule applies strictly. Beginner reader is not ready for aggressive CTAs.

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Motivation Block | Core | Narrative | Always fires. Before any instruction. User provides angle — Claude builds emotional case. If no angle — web search fills. Never fabricated. | None |
| Expectation Setting Block | Core | Narrative | Always fires. Difficulty level, realistic time investment, what reader can do after. Honest and specific — never vague reassurance. | None |
| Foundational Definition Block | Core | Structural | Always fires. Defines topic in plain language before instruction begins. | None |
| Progressive Instructional Sections | Core | Structural | Always fires. Each section assumes only what previous sections established. No jumping ahead. | None |
| Next Steps / Resources Block | Core | Structural | Always fires. Internal links primary. External resources — web search fills with credible named sources. Never a brand push. | None |
| Table of Contents | Conditional Core | Structural | Mandatory above 1,300 words. Optional below. Minimum 4 sections. | None |
| Glossary / Key Terms Block | Conditional Core | Structural | Fires as standalone section when topic requires 4+ foundational terms. Fires inline when fewer than 4. Critical terms always defined prominently regardless of count. | None |
| Challenges / Obstacles Block | Conditional Core | Narrative | Fires when topic has documented learning curve or known failure points. Placed before instructional sections. Drops when topic is simple enough that a challenges block would feel patronising. | None |
| Quick Win Block | Supporting | Narrative | Fires only when topic allows a meaningful immediate action — not for purely conceptual or theoretical topics. Placed after expectation setting. Shows early progress is possible. | None |
| Mental Model Block | Supporting | Narrative | Fires when topic involves multiple interacting concepts. Provides a simple framework to mentally organise information. Example: 'The 3 pillars of email marketing.' | None |
| Soft Mid-Guide CTA | Conditional Core | Narrative | Fires when brand has a tool or resource that directly supports a point being made. One mention maximum. Never in challenges section or glossary. | Cannot coexist with aggressive conclusion CTA. |
| Common Misconceptions Block | Supporting | Narrative | Fires when topic has widely held beginner misconceptions. Web search fills. **[F.0.3]** | None |
| Expert / Community Quote Block | Supporting | Narrative | Fires above 1,400 words. Named credible sources only. Never fabricated. **[F.0.3]** | None |
| Community Relevance Block | Supporting | Narrative | Fires when topic affects specific identifiable community. Web search fills. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Fires when user provides it or explicitly requests it. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 1,200 – 2,000 words |
| Floor | 900 words |
| Ceiling | 2,000 words. Above this → flag and offer reframe as F.12 |
| Open slot trigger | Above 1,800 words — ask what additional depth the user wants |
| Hard scope rule | If brief requires deep coverage of more than one sub-topic — flag immediately. Beginner's Guide goes wide and links. |

**Writing Rules:**
1. Motivation block comes before any instruction.
2. Table of contents fires above 1,300 words without exception.
3. Expectation setting is honest and specific. Vague reassurance is banned.
4. Every section assumes only what previous sections established. No jumping ahead.
5. Jargon defined on first use — always. No term used a second time without having been defined the first time.
6. The challenges block comes before instructional sections begin — not after.
7. The guide links out to depth rather than containing it.
8. The Quick Win Block — when it fires — comes immediately after expectation setting.
9. The Next Steps block is oriented entirely around the reader's journey — not the brand's conversion goals.
10. The close is forward-looking. Never a summary of what was just covered.

**Common Failure Modes:**
- Instruction begins before motivation is established
- Expectation setting replaced with vague encouragement
- Challenges block placed at the end instead of before instruction
- Guide goes deep on one sub-topic — becomes a How-to in disguise
- Jargon used before it is defined
- No TOC on guides above 1,300 words
- Next Steps block replaced with a brand CTA
- Progressive depth rule broken — section assumes knowledge not yet established
- Scope creeps above 2,000 words without flagging
- Quick Win included for a purely conceptual topic where no immediate action is possible

---

### F.12 — Ultimate Guide / Pillar Post

**Core Rule:** An Ultimate Guide is not a long blog post. It is a standalone comprehensive resource on one focused topic that makes every other article on that topic unnecessary. The reader arrives from any knowledge level and leaves with complete mastery. It is the hub of a content ecosystem — every related article links to it, it links to nothing deeper on the topics it claims to cover. Scope precision is everything.

**Humanise Pass:** Full Pass

**Tone Architecture:** Peer-level respect for the reader's intelligence and commitment. Technical terms used where they are the correct precise language — defined on first use, once, never repeatedly. The tone is authoritative without being academic, comprehensive without being exhausting.

Hard prohibition: Claude never writes to a word count. Every sentence earns its place or it is cut.

**Scope Check — Fires Before Any Generation:**

| Scope Issue | Claude Response |
|---|---|
| Too narrow | Flag and offer two options: broaden topic or switch to How-to. |
| Too broad | Flag and help user narrow to a focused achievable topic. |
| Above 10 sections | Advisory flag only — not a blocker. Claude asks for confirmation. Complex topics may genuinely need more sections. |
| Correct scope | One focused topic where comprehensive coverage is achievable in a single piece. Generation proceeds. |

**Default Structural Flow:**

| Stage | Content |
|---|---|
| 1. Definition and Scope | What this topic is and what this guide covers — and what it doesn't. |
| 2. Why It Matters | Stakes. Why mastering this topic is worth the reader's investment. |
| 3. Core Concepts | Foundational dimensions of the topic. Progressive depth within each. |
| 4. Advanced Concepts | Nuanced application. Assumes core concepts are understood. |
| 5. Practical Application | How to use what's been learned. Bridges theory and practice. |
| 6. Tools and Resources | Specific tools, platforms, resources needed to act on the guide. |
| 7. Common Mistakes | Documented failure modes with specific causes and fixes. |
| 8. Next Steps | What the reader does now. Where the content ecosystem extends. |

**TOC vs Roadmap — Explicit Distinction:**

| Component | Purpose | What It Contains |
|---|---|---|
| Table of Contents | Navigation structure | Section names and anchors. Reader jumps to sections. Always fires. |
| Roadmap Block | Learning journey explanation | 2–3 sentences explaining the shape of the guide, why it's structured this way, and what the reader will be able to do at each stage. Fires at 5+ sections. Not a summary — a navigation tool. |

**Internal Linking Rule:** Internal links in F.12 are primarily incoming — placeholders for other articles to link TO this guide. Outgoing internal links placed only for topics genuinely outside this guide's scope. External links: allowed only for authoritative references — research papers, government sources, industry standards. Never for deeper explanations of topics the guide claims to cover comprehensively.

**Sector Overlays:**
- IT/SaaS: Technical depth calibrated per section. Internal linking to product pages only when contextually justified.
- Legal/CA/Finance: YMYL. E-E-A-T signals critical. Author credentials mandatory. Disclaimer framing in relevant sections.
- Healthcare: YMYL. Medical disclaimer mandatory throughout entire guide. Anonymous author route not available.
- Marketing/Agency: Any statistics or case references must be sourced. Content gap sections especially valuable.

**Enrichment Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Comprehensive Headline | Core | Structural | Always fires. Must signal completeness. Two options generated: SEO-direct and authority-led. | None |
| Table of Contents | Core | Structural | Always fires. Minimum 5 sections. | None |
| Roadmap Block | Conditional Core | Structural | Fires when guide has 5+ major sections. Placed between TOC and first section. Navigation tool, not summary. | None |
| Foundational Definition Section | Core | Structural | Always fires. Defines topic precisely. Sets scope boundary — what this guide covers and what it doesn't. | None |
| Why This Matters Section | Core | Narrative | Always fires. Establishes stakes before depth begins. | None |
| Core Concept Sections | Core | Structural | Always fires. Each section covers one major dimension exhaustively. Each H2 optimised for secondary keyword target. | None |
| Subsection Rules Application | Core | Structural | Always fires. When a subsection is best structured as a How-to — How-to rules apply. When best as a listicle — listicle rules apply. F.12 orchestrates other subtypes as internal tools. | None |
| Concept Framework Block | Supporting | Narrative | Fires when topic involves multiple interacting concepts or dimensions. Provides structural model of the topic. | None |
| Practical Application Section | Conditional Core | Structural | Mandatory when topic has actionable dimensions. Bridges theory and practice. Drops only for purely theoretical topics. | None |
| Author Credentials Block | Conditional Core | Narrative | Mandatory for YMYL sectors. Supporting for all others. User must provide. | None |
| Data and Research Section | Supporting | Narrative | Fires when user provides original data or credible third-party stats exist. Named sources only. **[F.0.3]** | None |
| Case Study or Example Section | Supporting | Narrative | Fires when topic benefits from real-world proof. User provides own examples. Third-party — web search verifies. **[F.0.3 for third-party]** | None |
| Common Mistakes Section | Supporting | Narrative | Fires above 3,000 words. Specific documented failure modes with causes and fixes. Vague warnings banned. | None |
| Tools and Resources Section | Supporting | Structural | Fires when topic involves specific tools the reader needs. Web search verifies current relevance. **[F.0.3]** | None |
| FAQ Section | Supporting | Structural | Fires when SERP reveals PAA questions not already covered as major sections. | If Core Concept Sections already cover PAA questions — reduce to remaining only. |
| Expert Quote Block | Supporting | Narrative | Fires above 3,000 words. Named credible sources only. **[F.0.3]** | None |
| Community Relevance Block | Enrichment | Narrative | Fires when topic affects specific identifiable community. **[F.0.3]** | None |
| Author / Brand Bio | Enrichment | Narrative | Mandatory for YMYL (escalates to Conditional Core). Otherwise fires when user provides it. | None |

**Constraint Layer:**

| Parameter | Value |
|---|---|
| Natural range | 2,500 – 6,000 words |
| Floor | 2,500 words |
| Ceiling | None. Topic scope determines length. Every section must justify its existence. |
| Section minimum | 5 major sections |
| Section maximum flag | Above 10 sections — advisory flag only. Claude asks for confirmation. Not a blocker. |
| Hard rule | Every sentence earns its place or it is cut. Length is a product of completeness, not a target. |

**Depth Layering Rule:** Sections move progressively from foundational understanding to advanced application to practical implementation. Claude never jumps to advanced nuance before establishing the foundation within a section. This applies within each major section as well as across the guide as a whole.

**Writing Rules:**
1. Scope is confirmed before a single word is written.
2. The guide covers its stated topic exhaustively. If a section would require linking out to a deeper resource on a topic the guide claims to cover — that content belongs inside the guide.
3. Internal links point inward. Outgoing links placed only for topics genuinely outside this guide's scope.
4. Technical terms used where they are the correct precise language. Defined on first use — once. Never repeatedly explained.
5. Each H2 section treated as an individual secondary keyword ranking target.
6. When a section is best structured as a How-to — How-to writing rules apply. When best as a listicle — listicle rules apply.
7. The Roadmap block tells the reader the shape of the guide and why it's structured this way — before section one begins.
8. The Practical Application section bridges theory and practice. Shows the reader how to use what they've learned.
9. Common Mistakes section names specific documented failure modes with causes and fixes. Vague warnings banned.
10. The close orients the reader toward mastery — what they now know, what they can do, where the ecosystem extends. Never a summary.

**Common Failure Modes:**
- Scope too narrow — guide is a padded How-to with inflated title
- Scope too broad — covers everything at surface level, masters nothing
- Word count treated as a target — padding appears, quality drops
- Linking out from sections the guide claims to cover comprehensively
- Technical terms avoided to simplify — patronises the committed reader
- Technical terms used without definition
- Roadmap missing on a long guide — reader loses orientation
- Practical Application missing on an actionable topic
- Common Mistakes section filled with vague warnings instead of specific failure modes
- Guide ends with a sales close instead of a mastery orientation
- Incoming vs outgoing internal link rule violated

---

## Generation Checklist

Before finalising any blog post, confirm:

- [ ] Sector skill loaded and sector behaviours applied
- [ ] Correct humanise pass applied throughout (not as a separate edit pass — during generation)
- [ ] No banned phrases from G.3 present
- [ ] All Core components present for this subtype
- [ ] All high-risk / medium-risk components verified against F.0.3 rules
- [ ] No statistics, quotes, or expert attributions without a current-session web search source
- [ ] No content padded to hit a word count
- [ ] Global section maximum respected (8 H2 default, subtype exceptions noted)
- [ ] SEO elements present if Phase 4 fired: meta title, meta description, LSI keywords, internal link placeholders, schema notes
- [ ] Output format matches CONTENT_BRIEF.output_format
- [ ] YMYL sectors: author credentials present and disclaimer added
- [ ] Opinion subtype: no hedging in the body after a bold opener
- [ ] Case Study: no emotional or persuasive language anywhere
- [ ] Interview: quoted material not rewritten or smoothed
- [ ] Guest Post: no CTA in the conclusion
