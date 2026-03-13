# Social Media Generation Skill

**File:** skills/social/SKILL.md
**Role:** Generate social media content from a completed CONTENT_BRIEF.
Intake is already done. Phase 2 social routing questions are already answered.
This skill reads the brief, validates it, routes to the correct subtype, and generates.

**Do not ask intake questions. Do not re-run Phase 1–4. Generate.**

---

## Pipeline Position

```
/create → intake/SKILL.md → router/SKILL.md → social/SKILL.md ← you are here
                                                      ↓
                                              humanise/SKILL.md
```

Input: populated CONTENT_BRIEF
Output: completed social media content in CONTENT_BRIEF.output_format

---

## CONTENT_BRIEF Contract

Required fields for all social subtypes:

- `format` — must equal `social`
- `sub_type` — must match a value in the subtype whitelist
- `platform` — must be populated
- `topic` — must be non-empty
- `content_goal` — must be populated
- `target_audience` — must be populated

Additional required fields by family:

| Family | Additional required fields |
|---|---|
| Family 2 (Carousel, Story, Poll, Thread) | `unit_count` (integer or Claude-recommended) |
| Family 3 (Short Video Script, Long Video Script, LinkedIn Article) | `duration_target` |
| Short Video Script only | `delivery_mode` |
| LinkedIn Article only | `article_type` |

Optional fields used when present:

- `brand_name`, `brand_description`, `tone`, `angle`
- `content_points`, `cta_type`, `keyword`
- `hallucination_risk_components`, `context_material_used`
- `arc_type`, `sector`, `language`, `output_format`

If any required field is missing → Hard Stop before generation.

---

## Pre-Generation Sequence

Run these steps in order before writing any content.

**Step 1 — Confirm brief sufficiency.**
Run the Silent Layer check. If the brief fails, fire the Hard Stop before writing a single word.

**Step 2 — Validate format.**
If `CONTENT_BRIEF.format ≠ "social"` → Hard Stop. This skill only processes social format requests.

**Step 3 — Normalise and validate sub_type.**
Normalise `CONTENT_BRIEF.sub_type` to lowercase before comparison.
Validate against the subtype whitelist in the Subtype Routing section.
If the normalised value does not match → Hard Stop before generation.

**Step 4 — Validate platform.**
Normalise `CONTENT_BRIEF.platform` to lowercase before comparison.
Valid values after normalisation: `instagram` | `tiktok` | `youtube` | `linkedin` | `x-twitter` | `facebook` | `multi-platform`
If the normalised value does not match one of these → Hard Stop.

**Step 5 — Load sector skill.**
Read `sectors/{sector_id}/SKILL.md` before generating.

Resolve `sector_id` from this table only. Do not derive programmatically.

| CONTENT_BRIEF.sector | sector_id |
|---|---|
| IT / SaaS / Tech | `tech` |
| Legal / CA / Finance | `ca_legal-finance` |
| Healthcare | `healthcare` |
| Real Estate | `real-estate` |
| Education | `education` |
| E-commerce / Retail | `ecommerce` |
| Hospitality / Travel | `hospitality-travel` |
| Marketing / Agency | `marketing-agency` |
| HR / Recruitment | `hr-recruitment` |
| Manufacturing / B2B | `manufacturing-b2b` |
| Local Services | `local-services` |
| general | no sector skill loaded — proceed without overlay |

If `sector_id = general`: skip sector overlay behaviour. Continue using base subtype rules only.

**Step 6 — Determine humanise pass.**
Identify the HUMANISE_PASS using the table in the Humanise Pass Assignment section.
Attach HUMANISE_PASS as metadata to the output.
The actual transformation is performed by `skills/humanise/SKILL.md`. Do not apply it here.

**Step 7 — Identify hallucination-risk components.**
Check `CONTENT_BRIEF.hallucination_risk_components`.
Apply F.0.3 rules to every flagged component.

**Step 8 — Apply platform output rules.**
Apply the platform-specific output conventions defined in the Platform Output Rules section before generating.

**Step 9 — Execute subtype generation.**
Use the validated sub_type to activate the correct subtype rules.
Only one subtype rule set may activate. All other subtype sections are ignored.

---

## Silent Layer — Brief Sufficiency Check

Run before generating. Never user-facing unless Hard Stop fires.

| Insufficiency Condition | Hard Stop Response |
|---|---|
| Required field missing | Flag the specific missing field |
| Family 2 subtype: unit_count null | Apply default recommendation from Family 2 rules before generating. Hard Stop only when platform signal is also absent and no recommendation can be derived. |
| Family 3 subtype: duration_target missing | Flag — cannot translate to structure without it |
| Testimonial Post: no testimonial source material provided | Hard Stop — Claude does not write testimonials |
| Poll: no question provided | Hard Stop — Claude does not invent poll questions |
| LinkedIn Article: no thesis or topic direction | Hard Stop — topic coverage is not a thesis |

**Hard Stop response format:**

> "Before I start writing, I need to flag something. For a [sub_type] on [platform] I need [specific missing material] — without that I'd be making it up. Options: 1. Give me [specific ask]. 2. I can write what I have — but [specific component] will need to be completed by you."

---

## Cross-Subtype Principles

Apply to every social subtype without exception.

### Confirmed Zone / Intelligent Extension Zone

- **Confirmed Zone:** everything the user explicitly provided or confirmed. Written with full confidence, no hesitation.
- **Intelligent Extension Zone:** Claude extends with relevant, web-searched and verified supporting material — industry context, community signals, corroborating references.
- User-provided material from Pre-Phase 1 context extraction is confirmed zone — authoritative by definition.
- When `CONTENT_BRIEF.context_material_used = true`: treat all brand details as confirmed zone.
- If something feels like a stretch, it does not go in.

### Text-Only Generation

This plugin generates text content only. It does not generate images, graphics, or video frames.

For all visual social formats (Carousel, Story, Short Video Script, Long Video Script):
- Claude generates slide/frame copy, text overlay suggestions, and written descriptions of visual concepts.
- Scene direction for video scripts is textual production guidance only: visual concept notes, action/movement descriptions, on-screen text suggestions, transition/editing cues.
- No media assets are produced under any circumstances.

### Web Search Native

Cowork supports web search natively. Use web search for: community research, trend verification, competitor context, industry terminology, and any factual material the user has not provided.

**Confirmed Zone Precedence Rule:** Web search may only populate missing supporting material. User-provided facts in the Confirmed Zone are never overridden or contradicted by web search results. If a web search result conflicts with user-provided information — the user-provided information stands. Flag the conflict if it is material.

**User-Originated Content Guard:** Web search must never generate or supply content that must originate from the user's brief. These are hard prohibitions:
- Poll questions — must come from the user. Claude may suggest options for approval only when the user explicitly requests it.
- Testimonial quotes — must come from the user. Claude never writes or imports testimonial-sounding language from web search.
- Engagement questions (Engagement Post subtype) — the question must originate from the user's intent or be generated from the user's stated topic, not sourced from external content.

Web search failure behaviour: If web search fails or produces no credible sources:
- Drop the component entirely.
- Never fabricate statistics, claims, or quotes.
- Where applicable, insert a placeholder: `[Include your own source here — web search returned no credible results for this component]`.

### Single-Idea Rule (All Formats)

Every piece of social content communicates one idea. Not one topic — one idea. If the user's brief contains multiple competing ideas, flag it before generating and ask which to prioritise.

### One Primary Action Rule (All Formats)

Every piece of social content has one dominant action. Secondary soft engagement prompts are permitted only when they do not compete with the primary action. Two competing primary actions in the same post is a structural error. Flag before generating.

### Hook-First Structure

Every social output — post, sequence unit, or article — opens with the hook. Background, context, and credentials do not appear before the hook. No exceptions.

---

## Priority Tier System

Every component in every subtype library is assigned one of four tiers.

| Tier | Definition | Behaviour |
|---|---|---|
| **Core** | Must appear in every piece of this subtype | Missing info → Hard Stop or ask before generating. Never dropped. |
| **Conditional Core** | Mandatory only when a specific signal is detected | Behaves like Core when signal fires. Absent when signal is not present. |
| **Supporting** | Appears when information exists or format depth justifies it | No aggressive asking. Subject to selection cap. |
| **Enrichment** | Appears only when piece is long or user provides extra material | Dropped first when content is thin. |

---

## Sector Overlay Behaviour

Load the relevant sector skill file. The table below defines auto-behaviours that apply on top of subtype rules for every social subtype.

| Sector | Key Auto-Behaviours |
|---|---|
| IT / SaaS / Tech | Technical depth calibrated to audience knowledge. Jargon permitted for product-aware audiences. Outcome-first framing. |
| Legal / CA / Finance | YMYL. No legal advice language. Disclaimer auto-added where content touches regulatory topics. Credentialled framing only. |
| Healthcare | YMYL. No medical advice language. "Consult a professional" signal mandatory where relevant. Anonymous authority claims banned. |
| Real Estate | Location specificity mandatory. Local SEO signals active for LinkedIn Articles and long-form. |
| Education | Audience age/role calibration active. Outcome-focused framing. Board/curriculum specificity where relevant. |
| E-commerce / Retail | Product-first framing. Pricing accuracy critical — never approximate. Soft CTA on non-conversion posts. |
| Hospitality / Travel | Destination specificity. Seasonal framing optional. Experiential language. |
| Marketing / Agency | Results-first. Statistics must be sourced. "Results may vary" qualifier on client metrics. |
| HR / Recruitment | Role specificity. Compliance framing for legal regions. Inclusive language standards applied. |
| Manufacturing / B2B | Technical depth. Decision-maker framing. Jargon permitted. ROI and efficiency framing. |
| Local Services | City/suburb specificity mandatory. Trust-first tone. Service radius framing. |

---

## F.0.3 — Hallucination Window Rules

Applies system-wide across all social subtypes. The hallucination window opens in the Intelligent Extension Zone — when Claude populates components requiring supporting information not provided by the user.

**Risk classification:**

High-risk components — all three rules apply:
- Authority Quote Block / Expert Quote Block
- Data / Statistics Block
- Research Findings references
- Testimonials and client attributions

Medium-risk components — Rules 2 and 3 apply:
- Industry Context references
- Trend or community signals
- Competitor or market positioning claims

### Rule 1 — Source Verification Chain

For any component producing a factual claim sourced from outside the user's brief: trace the claim to a specific URL or named publication retrieved in the current session's web search. If the source cannot be traced to the current session — the claim does not go in. Training data recall is not a citable source in this plugin.

### Rule 2 — Grey Zone Drop Rule

When web search returns adjacent, outdated, low-quality, or conflicting results for a section requiring specific factual support — drop the section entirely rather than using adjacent material. Exception: include the section as a clearly marked placeholder — `[Include your own source here — I found conflicting data on this point]` — so the user knows the gap exists.

### Rule 3 — Synthetic Authority Signal

Hard stop during generation for these patterns without a traceable current-session source:
- "According to recent research..." without a traceable URL
- "Studies show..." without a named study
- "X% of [audience]..." without a traceable source
- "Experts agree..." without named experts
- "Industry data suggests..." without a named source
- Any statistic or percentage without a current-session web search citation

---

## Platform Output Rules

Platform is captured in `CONTENT_BRIEF.platform`. Apply these conventions at output time for all subtypes.

| Platform | Copy length convention | Hashtag count | CTA mechanics | Technical constraints |
|---|---|---|---|---|
| `instagram` | Visible window ~125 chars before truncation. Body up to 2,200. | 3–5 standard; more for reach-focused subtypes | Link in bio for external actions | Feed truncation at ~125 chars |
| `tiktok` | Short-form scripts: 40–225 words depending on duration | 3–5 | Follow, comment, duet | Video duration hard ceiling from `duration_target` |
| `youtube` | Scripts: see duration-to-structure tables | 3–5 | Subscribe, linked video, external resource | Chapter markers flagged for videos >8min |
| `linkedin` | Truncates at ~140 chars. Optimal long-form 1,000–1,500 words for articles | 3–5 | Comment, repost, follow, link out | Article register: professional editorial, not post register |
| `x-twitter` | 280 characters per post. Hard technical limit. | 1–2 | Reply, retweet, follow, link | 280 chars is a technical constraint, not a guideline |
| `facebook` | Optimal engagement under 80 chars. Up to ~63,000 chars max | 1–3 | Link, comment, share | Engagement drops sharply beyond 80 visible chars |
| `multi-platform` | Apply most restrictive platform's constraints | Use lowest common denominator count | Flag platform-specific mechanics user must adjust | Note which elements need per-platform adjustment |

**Feed Compression Rule — applies to all Family 1 and Family 2 outputs:**
The core value of the post — the primary message, offer, proof, or action — must appear within the visible window before platform truncation. The hook opens. The core value lands before the reader must expand. Posts that bury the primary message below the truncation boundary underperform regardless of hook quality.

**Hashtag logic:**
- Applied at output from platform signal. Not asked at intake.
- Hashtag count derived from platform table above.
- Standard mix: niche hashtag + brand hashtag + topic hashtag.
- Hashtag Campaign Post exception: campaign hashtag is primary; supporting hashtags secondary.

---

## CTA Behaviour by content_goal

| content_goal | CTA Behaviour |
|---|---|
| `educate` | Soft CTA. Suggest related content, follow for more, or save for later. No product push. |
| `build-authority` | Follow, newsletter, or profile CTA. Brand mention soft. |
| `convert` | Product or lead CTA. Direct action permitted for Promotional Posts, Landing-linked posts. |
| `persuade` | Soft-to-hard depending on subtype. Opinion injection follows subtype register. |
| `inform` | Soft or none. Information delivery is the primary action. |
| `entertain` | No CTA or very soft brand mention only. |
| `engage` | Question or poll mechanic is the CTA. No separate conversion CTA. Engagement Posts and Polls only. |

Subtype CTA rules override this table where they are more specific.

---

## Humanise Pass Assignment

Attach HUMANISE_PASS as metadata for `skills/humanise/SKILL.md`. Do not apply the transformation here.

| Subtype | HUMANISE_PASS | Key Restriction |
|---|---|---|
| Text Post | `full` | None |
| Image Caption | `full` | Caption must serve the visual — no over-explanation |
| Announcement Post | `full` | Clarity register: announcement leads, no burying the news |
| Promotional Post | `full` | No manufactured urgency injected during humanise pass |
| Engagement Post | `full` | Question tone preserved — no making it more formal |
| Event Post | `full` | Event details block excluded from rhythm adjustment |
| Testimonial Post | `restricted` | Customer voice preserved exactly. No rewriting, smoothing, or strengthening of quoted material |
| Hashtag Campaign Post | `full` | Campaign hashtag placement not altered |
| Carousel | `full` | Slide-level rhythm variation. Each slide treated as a discrete unit |
| Story | `full` | Frame copy compression preserved. No expanding minimal copy |
| Poll | `restricted` | Option parallelism must be preserved. No rewriting options |
| Thread | `full` | Per-post rhythm variation. 280-char ceiling not to be exceeded after pass |
| Short Video Script | `full` | Spoken register preserved. Scene direction notes excluded from humanise pass |
| Long Video Script | `full` | Spoken register preserved. Scene direction notes excluded. Mid-content retention mechanics not altered |
| LinkedIn Article (thought-leadership / personal-narrative / how-to) | `full` | Professional editorial register maintained |
| LinkedIn Article (industry-analysis / case-study) | `restricted` | Analytical register preserved. No opinion injection. No tone softening |

---

## SEO Behaviour

**Phase 4 is skipped for all social content without exception.**

SEO layer does not activate for any social subtype regardless of whether `CONTENT_BRIEF.keyword` is present.

Exception — LinkedIn Article with organic intent: if `CONTENT_BRIEF.keyword` is populated and the user has indicated organic search intent, apply the following silently:
- Keyword in article headline and first paragraph
- Keyword in at least one subheading if subheadings are used
- No keyword density engineering — keyword woven naturally only

No meta title, meta description, or schema markup is generated for social content.

---

## G.3 — Banned Phrases

**Category 1 — Generic AI-isms:**
In today's digital/fast-paced landscape, In the modern era, In the ever-evolving world of, It's important to remember that, It's important to note that, Unlock the secrets, Unleash your potential, Harness the power of, A testament to, Dive deep into, Delve into, At its core, At the end of the day, Comprehensive guide, Think of it as

**Category 2 — Forbidden Transitions:**
Furthermore, Moreover, In addition, Consequently, Additionally, Subsequently, Accordingly, Indeed, Not only...but also, In conclusion, To summarise, It goes without saying, Last but not least

**Category 3 — Lazy Adjectives:**
Revolutionary, Game-changing, Cutting-edge, Robust, Seamless, Synergy, Pivotal, Transformative, Innovative, Groundbreaking, Dynamic, Powerful, Vibrant, Crucial role, Ever-evolving, Nuanced approach

**Category 4 — Corporate Verbs:**
Leverage (use: use), Facilitate (use: help), Utilize (use: use), Endeavour, Underscore (use: show/prove), Optimize (use: improve), Revolutionize, Empower, Foster, Navigate (metaphorical), Circle back, Deep dive, Move the needle, Streamline

---

## Pre-Output Validation

Scan generated content for placeholder markers before producing final output.

If a placeholder remains:
- Notify the user that the section contains an unresolved placeholder.
- Do not output that section as final content.

Confirm before finalising any social content:

- [ ] `format` validated = `social`
- [ ] `sub_type` normalised and matched to whitelist
- [ ] `platform` validated and platform output conventions applied
- [ ] Sector skill loaded and sector behaviours applied
- [ ] Correct HUMANISE_PASS attached as metadata
- [ ] No banned phrases from G.3 present
- [ ] All Core components present for this subtype
- [ ] All high-risk / medium-risk components verified against F.0.3 rules
- [ ] No statistics, quotes, or testimonials without confirmed user-provided or current-session web search source
- [ ] No invented testimonials (Testimonial Post)
- [ ] No invented poll questions (Poll)
- [ ] Single-idea rule confirmed — only one central idea in the output
- [ ] One primary action confirmed — no competing CTAs
- [ ] Hook-first structure confirmed — hook appears before any background
- [ ] Feed Compression Rule confirmed — core value visible before truncation boundary (Family 1 and 2)
- [ ] 280-character limit confirmed per post (Thread only)
- [ ] Output format matches `CONTENT_BRIEF.output_format`
- [ ] YMYL sectors: disclaimer added where content touches regulatory or medical topics
- [ ] Visual references are textual descriptions only — no media generation

---

## Output Format Rules

Respect `CONTENT_BRIEF.output_format`.

| Value | Behaviour |
|---|---|
| `markdown` | Default. Structured markdown. Posts as paragraphs with hashtags on final line. |
| `plain-text` | No markdown syntax. Posts as plain paragraphs. |
| `cms-ready` | Markdown with placeholder labels for images/assets where applicable. |
| `script` | Two-column production format: spoken content left, scene direction right. Applies to Short Video Script and Long Video Script only. |

For Family 2 multi-unit outputs: present each unit as a numbered block (Unit 1, Unit 2, etc.) regardless of output_format.

For Family 3 video scripts: present as formatted script blocks — spoken line then scene direction note per beat.

---

## Subtype Routing

**Valid social sub_type values.** Normalise to lowercase before comparison.
If the normalised value does not match one of these → Hard Stop before generation.

**Family 1 — Single-Unit Posts:**
- `text-post`
- `image-caption`
- `announcement-post`
- `promotional-post`
- `engagement-post`
- `event-post`
- `testimonial-post`
- `hashtag-campaign-post`

**Family 2 — Multi-Unit Formats:**
- `carousel`
- `story`
- `poll`
- `thread`

**Family 3 — Long-Form & Scripted Content:**
- `short-video-script`
- `long-video-script`
- `linkedin-article`

**Subtype execution guard:** Only the rules belonging to the selected subtype are active. All other subtype rule sections are ignored.

| sub_type | Family | Active Section |
|---|---|---|
| `text-post` | Family 1 — Single-Unit Posts | S.1 |
| `image-caption` | Family 1 — Single-Unit Posts | S.2 |
| `announcement-post` | Family 1 — Single-Unit Posts | S.3 |
| `promotional-post` | Family 1 — Single-Unit Posts | S.4 |
| `engagement-post` | Family 1 — Single-Unit Posts | S.5 |
| `event-post` | Family 1 — Single-Unit Posts | S.6 |
| `testimonial-post` | Family 1 — Single-Unit Posts | S.7 |
| `hashtag-campaign-post` | Family 1 — Single-Unit Posts | S.8 |
| `carousel` | Family 2 — Multi-Unit Formats | S.9 |
| `story` | Family 2 — Multi-Unit Formats | S.10 |
| `poll` | Family 2 — Multi-Unit Formats | S.11 |
| `thread` | Family 2 — Multi-Unit Formats | S.12 |
| `short-video-script` | Family 3 — Long-Form & Scripted | S.13 |
| `long-video-script` | Family 3 — Long-Form & Scripted | S.14 |
| `linkedin-article` | Family 3 — Long-Form & Scripted | S.15 |

---

## Subtype Generation Rules

---

## Family 1 — Single-Unit Posts

**Family-level shared components. Apply to all Family 1 subtypes.**

| Component | Tier | Activation |
|---|---|---|
| Hook Block | Core | Always fires. First 1–2 lines. Must deliver post value before truncation. |
| Core Message Block | Core | Always fires. Body of the post. Length calibrated to platform and subtype. |
| Visual Direction Block | Conditional Core | Fires when user confirms a visual will accompany the post or when a visual was described/uploaded in Pre-Phase 1. Drops for text-only posts. Textual description only — written creative direction note. |
| CTA Block | Conditional Core | Fires by default. Drops only for pure social proof posts with no conversion action (Testimonial Post). |
| Hashtag Block | Supporting | Supporting by default. Conditional Core for Hashtag Campaign Post. Applied at output from platform signal. |

**Family 1 shared output constraints:**
- Single-idea rule enforced.
- Hook-first structure enforced.
- Feed Compression Rule enforced.
- Platform-adaptive copy length applied from platform signal.
- One primary action per post. Secondary soft engagement prompts permitted if non-competing.
- Hashtag logic applied at output, not asked at intake.

---

### S.1 — Text Post

**Phase 3 signals required:**
- Q1: What is this post trying to do — share an insight, tell a story, express an opinion, or something else? → register and Core Message approach
- Q2: Is this a standalone post or part of a content series? → fires or drops Series Continuity Signal

**Component activation:**

| Component | Behaviour |
|---|---|
| Hook Block | Most unconstrained hook in the family. Can be a provocative statement, counter-intuitive observation, or story opening. Hook quality carries the most weight — no offer, event, or question mechanic to fall back on. |
| Core Message Block | Register determined by Q1. No offer language, no product reference, no event details. If Q1 reveals promotional intent — route to Promotional Post. |
| CTA Block | Supporting for this subtype. Soft conversational close ("What's your experience with this?") often outperforms hard directional CTA. |
| Series Continuity Signal | Conditional Core. Fires when Q2 confirms a series. Hook must acknowledge continuity without requiring the reader to have seen previous posts. Each post in a series must work as a standalone entry point. |

**Constraints:**
- No offer language. No product reference. No event details. Promotional intent → route to Promotional Post before generating.
- If Q2 confirms a series: hook must work without prior context. Each post standalone.

---

### S.2 — Image Caption

**Phase 3 signals required:**
- Q1: Describe the image or provide it — what is it showing and what is the mood or moment it captures? → Hard Stop if not provided
- Q2: Should the caption lead the image (add context), follow the image (let visual speak), or reframe it (deliver a twist)? → structural relationship between Visual Direction Block and Core Message Block

**Component activation:**

| Component | Behaviour |
|---|---|
| Hook Block | Derived from the image, not invented independently. Must feel like a natural extension of what the image shows — or a deliberate tension against it (reframe approach). Hooks that ignore image content underperform. |
| Core Message Block | Inverted from family default. The image is the anchor. Core Message serves the visual, not the other way around. |
| Visual Direction Block | Drops entirely when the image already exists and was provided or described. Fires as written creative direction note only when the user has not yet created the visual. |
| CTA Block | Platform-dependent. Instagram: "link in bio" for conversion actions. Apply this convention automatically when platform is `instagram` and a conversion action exists. |

**Constraints:**
- Caption must not describe what the image literally shows unless the description adds meaning the image cannot deliver alone.
- Caption length is image-dependent. Self-evident images need minimal copy. Complex/ambiguous images may need more context.
- Q1 is a Hard Stop — generation cannot begin without image description or provided image.

---

### S.3 — Announcement Post

**Phase 3 signals required:**
- Q1: What are you announcing — product launch, new hire, partnership, milestone, or something else? → Core Message structure
- Q2: What is the key date or timeframe — happening now, soon, or retrospective? → urgency register
- Q3: What do you want people to do with this information — share it, act on it, or simply know about it? → CTA type and intensity

**Component activation:**

| Component | Behaviour |
|---|---|
| Hook Block | Must carry the announcement signal immediately. Reader knows what is being announced within the hook. Mystery or teaser hooks damage announcement posts — clarity is the trust signal. |
| Core Message Block | Structured around: What (the thing being announced) → Why it matters to the audience → When (timing/availability) → Where/How to access if applicable. Not all four always present — Q1 determines which apply. |
| CTA Block | Fires from Q3. If Q3 reveals awareness-only intent: CTA drops or becomes a soft share prompt. If Q3 reveals action intent: CTA is directional and specific. |

**Constraints:**
- Announcement must never be buried. Feed Compression Rule is especially critical: the announcement itself must appear before truncation.
- "Exciting news" as an opener without immediately delivering the news is banned (G.3 pattern).
- No hype language without substance.

---

### S.4 — Promotional Post

**Phase 3 signals required:**
- Q1: What is being promoted — a specific product, service, offer, or event? → Core Message anchor
- Q2: Is there a specific offer mechanic — discount, deadline, limited availability, bonus? → fires or drops Urgency/Scarcity Signal
- Q3: What is the single most important outcome the audience gets from this? → primary value proposition. If user provides multiple: ask them to rank, take top one only.

**Component activation:**

| Component | Behaviour |
|---|---|
| Hook Block | Benefit-led or tension-led. Feature-led hooks banned for promotional content. "Our product has X feature" is weaker than "Here's what changes when you use X." |
| Core Message Block | Fixed sequence: outcome first → mechanism second → offer/access point. Feature presentation before outcome context is not permitted. |
| CTA Block | **Mandatory. Core for this subtype. No exceptions.** If no CTA destination provided → Hard Stop before generating. |
| Urgency/Scarcity Signal | Conditional Core. Fires when Q2 confirms an offer mechanic exists. Appears within or adjacent to CTA Block. Urgency must be real — deadline, stock limit, or offer window must be genuine. Manufactured urgency (no actual limit) → flag to user before writing. |

**Constraints:**
- One offer per post. Two products or two offers simultaneously → flag and ask which to prioritise.
- Outcome before feature rule: features may appear only after outcome is established.
- CTA Block is Core — not Conditional Core. A promotional post without a CTA is structurally incomplete.

---

### S.5 — Engagement Post

**Phase 3 signals required:**
- Q1: What do you want to learn or start — genuine question to understand your audience, or prompting discussion to build community? → research-intent vs community-building
- Q2: What topic does this engage around — directly related to your brand, adjacent to your industry, or general audience interest? → brand connection determination

**Component activation:**

| Component | Behaviour |
|---|---|
| Hook Block | The question IS the hook in most cases. Open directly with the question. Preamble before the question kills engagement — banned. |
| Core Message Block | Compressed. One to two sentences maximum after the question. Adds context or stakes — why this question matters. More than this shifts format toward Text Post. |
| CTA Block | **Drops for this subtype.** The question is the CTA. A separate directional CTA competes with the question and dilutes engagement signal. Exception: if Q1 reveals a secondary conversion action — flag as structural risk before including. |

**Constraints:**
- One question per post. Two questions split attention and reduce response rate. If user drafts two questions → ask which is primary.
- Question must be answerable in a comment in 1–3 sentences. Abstract, multi-part, or overly complex questions fail as engagement posts. Claude evaluates question quality at generation and rewrites if too complex.
- Brand connection rule: post must have visible connection to brand's world. Pure lifestyle questions with no brand relevance → advisory flag, not a hard stop.

---

### S.6 — Event Post

**Phase 3 signals required:**
- Q1: What is the event — type, name, and what someone will experience or gain by attending? → Core Message anchor. Experience/gain element required — without it the post is a calendar entry, not a persuasive post.
- Q2: What are the key logistics — date, time, location or platform, registration link or access method? → fires Event Details Block. All four elements required for a complete event post.
- Q3: Is this post promoting the event (pre-event) or reporting on it (post-event)? → tense, urgency register, CTA type.

**Component activation:**

| Component | Behaviour |
|---|---|
| Hook Block | Must establish stakes — why this event matters to the audience specifically. Generic "join us" hooks banned. "What you'll walk away with" or "why this one is different" are stronger anchors. |
| Core Message Block | Experience/gain first, logistics second. Logistics without context are not persuasive. |
| Event Details Block | Core. Contains date, time, location/platform, registration method as a clean scannable unit. Presented separately from Core Message narrative — event logistics must not be buried in flowing copy. |
| CTA Block | Pre-event: registration or RSVP action. Post-event: tag someone who should attend next time, or follow for future events. Register applied from Q3 automatically. |

**Constraints:**
- Logistics completeness: if any of the four logistics elements are missing → flag before generating. Generation proceeds but missing elements are noted. Claude never invents missing logistics.
- Post-event variant: no urgency language. "Don't miss out" in a recap post is inaccurate and damages trust. Post-event uses reflection and proof framing only.

---

### S.7 — Testimonial Post

**Phase 3 signals required:**
- Q1: Provide the testimonial — the actual customer words, not a description of what they said. → Hard Stop if not provided. Claude does not write testimonials.
- Q2: Who is this customer — name, role, company, or any context that makes the testimonial credible? → drives Attribution Block. If user cannot provide → flag credibility risk, do not refuse.
- Q3: What result or transformation does this testimonial demonstrate? → framing focus for the post.

**Component activation:**

| Component | Behaviour |
|---|---|
| Hook Block | Two valid approaches: pull-quote hook (open with the most compelling line from the testimonial itself), or framing statement hook (contextualise the result before the testimonial appears). Default to pull-quote approach unless Q3 reveals a result more powerful as a framing statement. |
| Core Message Block | Source material is customer voice, not brand-generated copy. Claude's role is framing, editing for social format, and attribution. Customer's words are preserved. Claude may trim for length but must not alter meaning or add claims not in the original. |
| Attribution Block | Conditional Core. Fires when Q2 provides attribution details. Contains name, role/company, optional context note. Placement: after the testimonial, before the CTA. Never before the testimonial. |
| CTA Block | Soft register only. Acceptable: "Read more stories like this" / "See how we helped [sector]" / "Learn more about [service]." Banned: "Book now" / "Buy today" / "Limited time offer." Testimonial is doing the persuasion work — CTA opens a door, does not push through it. |

**Constraints:**
- No invented testimonials. Absolute rule, no exceptions. If no source material → Hard Stop.
- Integrity preservation: Claude may edit for platform length but must preserve meaning, voice register, and specific claims. Adding superlatives or strengthening claims is prohibited.
- Credibility flag: if testimonial contains extraordinary or unverifiable claims → flag before posting. User decides whether to proceed.
- HUMANISE_PASS for this subtype = `restricted`. Quoted material is never rewritten, smoothed, or rhythm-adjusted.

---

### S.8 — Hashtag Campaign Post

**Phase 3 signals required:**
- Q1: What is the campaign hashtag and what does the campaign stand for? → structural anchor. If no campaign hashtag exists yet: Claude may suggest one for approval before proceeding. This is the only social subtype where Claude may generate a naming suggestion as part of intake.
- Q2: Is this an originating post (launching the campaign) or a participation post (contributing to an existing campaign)? → structural difference determines entire output shape.
- Q3: What action do you want people to take — use the hashtag themselves, submit content, share something, or follow along? → campaign participation mechanic and CTA structure.

**Component activation:**

| Component | Behaviour |
|---|---|
| Hook Block | Originating posts: campaign-identity-first. Hook must introduce the campaign concept clearly enough that someone who has never seen it understands what it is and why — within the visible window. Participation posts: hook can assume familiarity and lead with contribution framing. |
| Core Message Block | Originating posts: campaign purpose → participation mechanic → brand connection. Participation posts: the specific contribution this post makes to the campaign narrative. |
| Campaign Identity Block | Core. Contains primary campaign hashtag (prominent placement), one-line campaign description for originating posts, and participation CTA. Placement: end of post or closing beat in Core Message. Never buried mid-copy. |
| CTA Block | Participation-mechanic-driven. "Use [#CampaignHashtag] to share your [X]" is the structural model. CTA and campaign hashtag are inseparable in originating posts. |
| Hashtag Block | Campaign hashtag is Conditional Core — not Supporting. Supporting hashtags (topic, sector, brand) are secondary. Campaign hashtag appears first and separately from supporting hashtags where platform convention allows. |

**Constraints:**
- Campaign hashtag must be unique enough to track and specific enough to understand. Generic hashtags (#mondaymotivation, #instagood) as campaign hashtags are flagged — too broad to own or track.
- Originating posts must contain the participation mechanic. If the post does not explain what the audience should do with the hashtag → flag before generating.

---

## Family 2 — Multi-Unit Formats

**Family-level shared components. Apply to all Family 2 subtypes unless explicitly overridden.**

| Component | Tier | Activation |
|---|---|---|
| Sequence Hook Unit | Core | Always fires. First unit. Earns the commitment to progress — not only stops the scroll. Must deliver enough value/tension to compel progression AND signal what the sequence contains. |
| Content Units | Core | Always fires. The middle units. Each delivers one discrete value beat. Unit count from `CONTENT_BRIEF.unit_count`. |
| Transition Logic | Conditional Core | Fires for Carousel, Story, Thread. Does not apply to Poll. Embedded as closing beat of each Content Unit — not a separate output block. Creates pull from each unit to the next. |
| Closing Unit | Core | Fires for Carousel, Story, Thread. Does not apply to Poll in standard form. Lands the arc, delivers resolution, contains CTA. No new information in the Closing Unit. |
| Sequence CTA | Conditional Core | Lives in Closing Unit. Fires when sequence purpose includes conversion, subscription, engagement, or directed action. Drops for purely informational sequences. One primary action only. |

**Family 2 shared output constraints:**
- Progressive value rule: every unit earns its place. A unit that restates the previous unit or adds nothing is a drop-off point. Reduce unit count rather than pad.
- Sequence Hook Unit rule: hook earns commitment to progress. Audience must know what they are committing to before moving forward.
- Unit self-sufficiency standard: each unit remains interpretable if a reader resumes mid-sequence. Units may rely on prior context but must not require the audience to recall earlier units in detail.
- CTA placement rule: CTA belongs in the Closing Unit only. Mid-sequence micro-CTAs (save this / tap to see more) permitted only when they facilitate continued consumption — not conversion.
- Unit count control: Claude does not generate a sequence without confirming or recommending a unit count. If `unit_count` is null, apply these defaults:

  | Subtype | Default recommendation |
  |---|---|
  | Carousel | 5–7 slides |
  | Story | 4–6 frames |
  | Thread | 5–10 posts |
  | Poll | 2–4 options |

  State the recommendation before generating. Proceed unless the user overrides it.
- Visual references are textual descriptions only — no media generated.

---

### S.9 — Carousel

**Phase 3 signals required:**
- Q1: What format does this carousel follow — educational (step-by-step), narrative (telling a story), list-based (parallel items of equal weight), or argument (building a case toward a conclusion)? → arc type and generation logic
- Q2: How many slides do you want — and does the last slide need to drive a specific action, or close the idea? → unit count plus closing intent
- Q3: Does each slide need a visual concept described, or is this copy-only output? → fires or drops Visual Concept Notes per slide

**Component activation:**

| Component | Behaviour |
|---|---|
| Sequence Hook Unit | Slide 1. Must accomplish three things: stop the scroll, state the carousel's value proposition, signal the arc type. Hook logic varies by Q1 format: Educational → problem/promise hook. Narrative → inciting moment. List → state the list's value ("5 things that changed how I think about X"). Argument → thesis or counter-intuitive claim. |
| Content Units | One idea per slide. Slide copy: headline + 2–4 supporting lines maximum. Claude generates per slide: slide headline, slide body copy, and if Q3 confirms — a written visual concept note. Copy length is platform-adaptive: LinkedIn carousels tolerate more copy per slide than Instagram carousels. |
| Transition Logic | Embedded as closing line per slide creating pull to the next. Educational → next-step anticipation. Narrative → unresolved tension. List → momentum language. Argument → consequence escalation. |
| Closing Unit | Final slide. Three valid structures: Summary close (restates core takeaway in one line), Transformation close (states what audience now knows/can do), Action close (directs immediately to Sequence CTA). Q2 closing intent determines which applies. |
| Sequence CTA | Lives on closing slide. Platform-adaptive: Instagram → save, share, follow, link in bio. LinkedIn → comment, repost, follow. |

**Constraints:**
- Slide count ceiling: 10 slides advisory maximum. Above 10 → flag completion rate risk and recommend restructuring into shorter carousel plus companion post. Generation proceeds on user confirmation.
- One idea per slide is absolute. If arc cannot fit at one idea per slide → recommend increasing slide count or pruning arc, not doubling up.
- Slide 1 must not require swiping to understand. All value to decide engagement lives on slide 1.
- Platform cover slide distinction: Instagram → slide 1 functions as a grid tile as well as feed post. Headline must work standalone. LinkedIn → slide 1 is feed only — different constraint applies.

---

### S.10 — Story

**Phase 3 signals required:**
- Q1: What is the purpose of this Story sequence — behind the scenes, product/service walkthrough, announcement teaser, tutorial, or engagement prompt? → arc type
- Q2: How many frames do you need, and is there a hard time or tap limit to consider? → unit count and copy density calibration
- Q3: Does each frame have a defined visual — a photo, video clip, or graphic — or do you need a visual concept described for each? → determines output layers per frame
- Q4: Is there an interactive element on any frame — poll sticker, question sticker, swipe-up/link sticker? → fires Interactive Element Block

**Component activation:**

| Component | Behaviour |
|---|---|
| Sequence Hook Unit | Frame 1. Stories have the most aggressive consumption window — decision in under 2 seconds. Frame 1: minimal copy (1–3 words headline or single provocative line) + written visual concept note. Claude generates: minimal headline copy, written visual concept note, and if Q4 reveals a sticker on Frame 1 — sticker type and placement note. |
| Content Units | Each frame delivers one moment, not one argument. Maximum 2 lines of copy per content frame — enforced at generation. Extended copy on a Story frame is a structural error. |
| Transition Logic | Taps, not swipes. Each frame implies continuation through visual continuity notes (consistent colour, character, or setting) and copy tone that suggests movement rather than closure. Embedded in visual concept notes and frame copy. |
| Closing Unit | Final frame. If Q4 confirms an interactive element on final frame: Closing Unit integrates Interactive Element Block. Copy is directional and minimal — supports the interaction mechanic. |
| Sequence CTA | Embedded in or around Closing Unit. Platform-specific: Instagram → link sticker or "link in bio" direction. If no interactive element: CTA is copy-only direction on final frame. |
| Interactive Element Block | Conditional Core. Fires when Q4 confirms an interactive element on one or more frames. Output per element: element type (poll sticker, question sticker, link sticker, countdown timer), placement note, and copy for the element itself. Generates copy and placement direction only — not the interaction functionality. |

**Constraints:**
- Frame count advisory ceiling: 8 frames. Above 8 → flag completion rate risk. Generation proceeds on user confirmation. Claude recommends splitting into two-part Story series rather than extending single sequence.
- Copy density per frame is enforced: maximum 2 lines of copy per content frame. If arc cannot fit within this → recommend restructuring the arc, not overloading frames.
- Stories are ephemeral by default (disappear after 24 hours unless saved to Highlights). If sequence purpose implies evergreen content → flag format mismatch. Suggest Carousel or web page instead. Flag only — do not refuse.
- Visual concept notes are frame-level, not sequence-level. Each frame gets its own written visual description.

---

### S.11 — Poll

**Poll is a compressed multi-unit subtype. Transition Logic and Closing Unit do not apply.**

**Phase 3 signals required:**
- Q1: What is the poll question — stated exactly as you want it to appear? → Hard Stop if not provided. If user wants Claude to suggest: confirm topic and intent first, generate suggestion for approval, then proceed.
- Q2: How many options do you need — and what are they, or should Claude suggest options based on the question? → Content Unit count and option copy

**Component activation:**

| Component | Behaviour |
|---|---|
| Sequence Hook Unit | The question itself is the hook unit. No separate hook copy. Question must be clear, specific, and answerable within a single tap. Claude evaluates question quality before generating options — questions too broad, too academic, or requiring extended consideration are flagged. |
| Content Units | The answer options. 2–4 options. Each option must be: mutually exclusive, parallel in grammatical structure, meaningfully distinct. Options that are nearly synonymous waste a choice slot. Claude enforces structural parallelism — if user-supplied options are not grammatically parallel, Claude corrects and notes the change. |
| Transition Logic | Does not apply. |
| Closing Unit | Does not apply in standard form. The option set is the terminal structure. |
| Sequence CTA | Conditional. Most polls do not need an explicit CTA — voting is the action. Fires only when sequence purpose confirms a follow-up action (commenting with more detail, waiting for a results post). |

**Constraints:**
- Question clarity is absolute. Ambiguous question that can be interpreted two ways → flag before generating options.
- Option count: minimum 2, maximum 4. Five or more options → flag and recommend restructuring as Engagement Post question instead.
- No leading options. Options must not telegraph the "correct" answer or nudge toward a preferred response.
- Platform option count ceiling: X/Twitter supports up to 4 options. Instagram polls support exactly 2 options. LinkedIn supports up to 4. Facebook supports unlimited but 2–4 performs best. Apply correct ceiling from platform signal and flag if user's requested count exceeds it.

---

### S.12 — Thread (X / Twitter)

**Phase 3 signals required:**
- Q1: What is the core argument, insight, or story this thread communicates — stated in one sentence? → thread thesis. If user cannot state in one sentence → ask for refinement before proceeding.
- Q2: How many posts do you want in the thread — and do you want the thread numbered explicitly? → unit count plus numbering convention
- Q3: Should each post in this thread work as a standalone tweet — or are some posts designed to be read only in sequence? → standalone standard for Content Units

**Component activation:**

| Component | Behaviour |
|---|---|
| Sequence Hook Unit | Post 1. Must communicate the thread's full value proposition in 280 characters AND create enough tension/curiosity that expanding feels necessary. Hook post that resolves thesis entirely removes reason to read on. Hook post too vague fails to earn expansion. Claude generates the hook post last (after drafting the full thread), presents it first in output. |
| Content Units | Individual posts. 280-character hard ceiling per post — technical platform constraint, enforced absolutely. If content requires more than 280 characters: split into two posts. Each post ends with either a complete thought or a deliberate open loop pulling to the next post. No half-finished sentences running into the next post. |
| Transition Logic | Most explicit of all Family 2 subtypes. Final line of each post (except closing) does one of three things: creates an open loop (question the next post answers), escalates stakes (raises consequence of argument), or signals continuation explicitly ("Here's why this matters —"). Claude generates transition logic as the final line of each Content Unit explicitly — not embedded and assumed. |
| Closing Unit | Final post. Restates thesis from Post 1 in different language — synthesis, not copy-paste. CTA lives in closing post. Common closing structures: Thesis restatement + CTA, Call to action + invitation ("What would you add?"), Resource link + follow prompt. |
| Sequence CTA | Closing post. Platform-native options: retweet, reply, follow, or link out. One primary action only. Authority-building thread → follow CTA is primary. External content thread → link is primary. Derived from sequence purpose. |
| Thread Numbering Block | Conditional Core. Fires when Q2 confirms numbered thread format. Applies consistent numbering convention across all posts (1/, 2/, 3/ or 1. 2. 3. per user preference). If unnumbered: drops. Transition logic carries sequential coherence instead. |

**Constraints:**
- **280-character ceiling per post is absolute. Technical platform constraint.** Character counting includes hashtags, numbering (1/, 2/, 3/), punctuation, and spaces — everything counts. Claude counts characters at generation and splits any post that exceeds the limit. Splits do not occur mid-sentence — each post ends on a complete thought or deliberate open loop.
- Hook post drafted last, presented first. Generation sequence rule — not output order rule.
- Thread thesis must be resolvable in the unit count. Complex thesis in 5 posts will be underdeveloped. Simple insight in 15 posts will be padded. Claude evaluates fit and flags mismatch before generating.
- Standalone standard from Q3 applied at generation. If standalone required: each Content Unit communicates its point without requiring the previous post to be remembered.
- No sub-threading. One thesis, one progression, one resolution. Branches that never return to the main thesis are flagged.

---

## Family 3 — Long-Form & Scripted Content

**Family-level shared components. Apply to all Family 3 subtypes unless explicitly overridden.**

| Component | Tier | Activation |
|---|---|---|
| Opening Hook | Core | Always fires. Earns continued consumption before the first natural exit point. Scripts: works within first 5–15 seconds of delivery. LinkedIn Articles: works within first 2–3 sentences before "see more" break. |
| Core Sections | Core | Always fires. Section count derived from duration/length target and thesis complexity. Each section advances the thesis by one meaningful step. Claude determines section count before generating section content. |
| Supporting Examples or Beats | Conditional Core | Fires when Core Sections contain abstract argument or instruction requiring grounding. Drops only for content that is entirely concrete throughout. Claude evaluates per section at generation — abstract sections without a grounding element are flagged. |
| Closing Resolution | Core | Always fires. Lands the thesis, delivers earned conclusion, transitions to CTA. No new information in the close. Three valid structures: Synthesis close, Transformation close, Provocation close. |
| Call-to-Action | Conditional Core | Fires when content objective includes conversion, subscription, engagement, or directed action. Drops for purely informational or thought leadership content where directed action undermines the editorial register. One primary action only. |
| Scene Direction Layer | Conditional Core | Fires for Short Video Script and Long Video Script only. Does not apply to LinkedIn Articles. Output: visual concept notes, action/movement descriptions, on-screen text suggestions, transition/editing cues. All outputs are textual production guidance only — no media generated. |

**Family 3 shared output constraints:**
- Single-thesis coherence rule: every section connects back to the central thesis. Sections that do not advance the thesis are flagged as drift risk before inclusion.
- Progressive development rule: each section leaves the audience further along than the previous one. Sections that restate earlier points without adding new dimension are padding.
- Closing resolution requirement: the close must resolve. A close that trails off or ends abruptly is a structural failure.
- Pacing architecture rule: dense informational sections must be balanced with grounding elements — examples, stories, demonstrations, application beats. Long runs of abstract explanation are broken before the next dense section begins. For scripts: grounding elements also function as visual relief beats.
- Duration-to-structure translation: `CONTENT_BRIEF.duration_target` is converted to generation parameters before writing begins. See translation tables in each subtype section.

---

### S.13 — Short Video Script (Reels / TikTok)

**Duration-to-structure translation. Convert `CONTENT_BRIEF.duration_target` before generating.**

| Duration | Approximate Word Count | Structural Beats | Pacing |
|---|---|---|---|
| 15–30s | 40–75 words | 2 (Hook + Close) | Maximum compression |
| 30–60s | 75–150 words | 3 (Hook + Setup + Payoff + Close) | High compression |
| 60–90s | 150–225 words | 4 (Hook + Setup + Payoff + Close) | Moderate compression |

Spoken delivery baseline: 130–150 words per minute. Validate generated word count against duration before output. If word count exceeds delivery window → flag before delivering script.

**Internal arc for all short video scripts:**
`Hook → Setup → Payoff → Close`
- Hook: captures attention within first 2 seconds of delivery (spoken or visual)
- Setup: introduces the single idea — no new points, no tangents
- Payoff: demonstrates or proves the idea established in Setup
- Close: lands the takeaway and delivers CTA if applicable

**Phase 3 signals required:**
- Q1: What is the hook moment — what single thing stops someone mid-scroll in the first two seconds? → hook mechanism
- Q2: What is the one thing the audience walks away knowing or doing — stated in a single sentence? → single takeaway. If user provides more than one → flag and ask them to choose before proceeding.
- Q3: Who is delivering this — on-camera speaking, voiceover only, or text-on-screen only? → determines Scene Direction Layer structure. Populates `delivery_mode`.
- Q4: Is there a platform-specific format requirement — TikTok trending audio, Reel template structure, aspect ratio constraint? → fires Platform Format Note

**Component activation:**

| Component | Behaviour |
|---|---|
| Opening Hook | Must capture attention within first 2 seconds of delivery. Both spoken and visual hooks qualify. 2-second rule is absolute — flag any hook that cannot be delivered in under 2 seconds and offer a tighter version before proceeding. |
| Core Sections | Hook → Setup → Payoff → Close arc. Maximum two Core beats (Setup + Payoff) for scripts under 90 seconds. Most under 60 seconds carry only one Setup-Payoff cycle. Each Core Section: one point, demonstrated or stated, with one supporting example or visual beat. No Core Section should run longer than 20–25 seconds of delivery time. |
| Supporting Examples or Beats | Compressed to a single sentence or single scene direction note. Abstract examples requiring more than one sentence of setup → replace with more direct grounding element. |
| Closing Resolution | Final 5–10 seconds. Land the single takeaway. No summary. Closing options: restate hook's promise as fulfilled, deliver punchline/resolution, or go directly to CTA. |
| Scene Direction Layer | Fires for every script beat. Output per beat: spoken line(s), scene direction note, on-screen text overlay if applicable, transition cue between beats. On-camera mode: speaker blocking notes + eye-line direction. Voiceover mode: spoken lines + B-roll description notes. Text-on-screen mode: title card copy + visual background description. Script presented as formatted production document — spoken content and direction notes clearly separated. |
| Call-to-Action | Spoken + on-screen text overlay. Delivery window: 3–5 seconds maximum. One action only. Platform-native: TikTok → follow, comment, duet. Instagram Reels → save, share, profile visit. Link-in-bio for external actions. |

**Constraints:**
- Two-second hook rule is absolute. Any hook requiring more than 2 seconds to deliver → flag and offer tighter version.
- Format mismatch: if Q2 reveals a content objective requiring more than one Setup-Payoff cycle → flag format mismatch. Recommend Long Video Script. Wait for user confirmation before proceeding either way.
- Scene direction must be realistic for a solo creator unless user has indicated production resources are available. Default to single-location, single-subject direction.

---

### S.14 — Long Video Script (YouTube)

**Duration-to-structure translation. Convert `CONTENT_BRIEF.duration_target` before generating.**

| Duration | Approximate Word Count | Section Count | Pacing |
|---|---|---|---|
| 5–8 min | 750–1,200 words | 4–5 sections | Moderate — one grounding element per section |
| 8–15 min | 1,200–2,250 words | 5–7 sections | Balanced — developed arguments with regular grounding |
| 15–20 min | 2,250–3,000 words | 7–9 sections | Dense — full argument development with examples and application beats |
| 20+ min | 3,000+ words | 9+ sections | Extended — requires mid-content retention mechanics |

Spoken delivery baseline: 130–150 words per minute. Validate word count against duration. Overage → flag before output.

**Phase 3 signals required:**
- Q1: What is the video's central argument or primary value — what does the audience leave with that they did not have at the start? → thesis. Must be statable in two sentences. Ask for refinement if not.
- Q2: What structure does this video follow — tutorial (step-by-step), essay (argument developed across sections), listicle (parallel items), story-driven (narrative arc with lesson), or documentary-style (evidence building a case)? → Core Section architecture and pacing logic
- Q3: Who is the target viewer — and what do they already know about this topic? → audience knowledge level and vocabulary calibration
- Q4: Does this video need a hook sequence before the main content — and should there be an intro/channel branding section? → fires or drops Intro Block
- Q5: Are there specific sections, examples, or demonstrations that must be included? → fires Mandatory Content Block

**Component activation:**

| Component | Behaviour |
|---|---|
| Opening Hook | YouTube drop-off is highest in first 30 seconds. Hook must deliver a preview of the video's payoff within the first 30 seconds of script. Two valid approaches: Payoff Preview ("By the end of this video, you'll understand exactly why X") — applies to tutorial and essay structures. Problem Agitation (establish the problem with enough specificity that the target viewer recognises themselves) — applies to story-driven structures. |
| Core Sections | Multi-section architecture with explicit section headers in script output. Section length calibrated from translation table. Claude states the proposed section architecture before generating full script content for scripts above 8 minutes. User confirms or adjusts structure, then Claude generates. |
| Supporting Examples or Beats | Fully developed in this subtype — complete stories, case references, demonstrations, application exercises. Tutorial structures: demonstrations are the grounding element. Essay structures: case references or analogies. Story-driven: narrative provides the grounding. |
| Mid-Content Retention Mechanics | Conditional Core. **Non-negotiable for scripts above 8 minutes.** Mechanisms: mid-video hook ("Stay to the end — the most important part is coming up"), pattern interrupt (shift in delivery register, direct question to viewer, surprising reframe), or progress signal ("We've covered X and Y — now for the part most people miss"). At least one retention mechanic per 5-minute block of content after the opening. |
| Intro Block | Conditional Core. Fires when Q4 confirms intro is needed. Contains channel/creator introduction, topic framing, and what the video covers. Placement: after Opening Hook, before first Core Section. Maximum 30–45 seconds of delivery time. Drops entirely when Q4 confirms no intro needed. |
| Mandatory Content Block | Conditional Core. Fires when Q5 confirms required sections/examples/demos. These are integrated into the section architecture during planning, not bolted on afterward. |
| Closing Resolution | Three-part structure: Summary beat (brief restatement of core argument — not full recap, a pointed synthesis) → Transformation statement (what viewer now knows or can do) → CTA sequence. |
| Scene Direction Layer | Fuller than Short Video Script — longer duration requires more visual variation. Output per section: spoken content, scene direction note (on-camera instruction or B-roll description), on-screen text overlays (titles, data callouts, chapter markers), transition cues. Chapter markers flagged in scene direction for videos above 8 minutes — timestamps to be added by creator during upload. |
| Call-to-Action | YouTube CTA stack: one primary action + subordinate secondary prompts. Primary action determined by content objective: subscribe (audience growth), linked video (retention), external resource (conversion). Secondary prompts (comment invitation, notification bell, watch next video) follow in subordinate position. Primary action stated first, given most prominent emphasis, placed closest to Closing Resolution. CTA stack presented in explicit priority order with end card placement notes. |

**Constraints:**
- Section architecture presented before full generation for scripts above 8 minutes. This is a process gate — not optional.
- Mid-content retention mechanics are non-negotiable above 8 minutes. Claude does not skip them.
- CTA stack hierarchy must be explicit. Secondary actions must not compete with primary in emphasis or placement.
- If generated word count exceeds delivery window → flag overage and identify which sections carry excess before asking user to trim or extend duration target.

---

### S.15 — LinkedIn Article

**Length-to-structure translation. Convert `CONTENT_BRIEF.duration_target` (word count format) before generating.**

Note: `duration_target` is the shared CONTENT_BRIEF field used across all Family 3 subtypes. For LinkedIn Articles it carries a word count value (e.g. `800w`, `1500w`), not a time value. This is the same field — the format of the value differs by subtype. The router and intake do not use a separate field for this.

| Length Target | Approximate Word Count | Section Count | Pacing |
|---|---|---|---|
| Short | 400–600 words | 3 sections | High compression — one example per section maximum |
| Standard | 800–1,200 words | 4–5 sections | Balanced — one developed example or case reference per section |
| Long | 1,500–2,000 words | 6–8 sections | Full development — extended examples, data references, application beats |
| Pillar | 2,000–3,000 words | 8–10 sections | Maximum development — complete argument with multiple supporting layers |

**Phase 3 signals required:**
- Q1: What is the central argument or professional insight this article makes — stated in one to two sentences? → editorial thesis. Claude evaluates: a topic statement ("an article about leadership") is returned with a request to sharpen into a specific claim. Generation does not begin until a defensible thesis exists.
- Q2: What type of article is this — thought leadership (professional opinion/argument), how-to guide (practical instruction), industry analysis (trends/data/sector commentary), personal narrative (experience-based insight), or case study (specific example with transferable lesson)? → Core Section architecture and editorial register. Populates `article_type`.
- Q3: What professional context does the reader bring — what do they already know, and what assumption does the article challenge or confirm? → audience context and section setup requirements
- Q4: Should this article reference any specific data, research, personal experience, or case examples — and if so, what are they? → fires Source Material Block. If user has none → flag that article will read as generic and advise adding at least one specific grounding reference.

**Component activation:**

| Component | Behaviour |
|---|---|
| Opening Hook | LinkedIn truncates article previews at ~150–200 characters before "see more." Opening sentence must work as a standalone statement compelling click-through. Hook mechanisms by article_type: Thought leadership / personal narrative → Contrary claim ("Everything you've been told about X is optimised for the wrong outcome") or Specific observation ("In the last 12 months, I've reviewed 200 proposals. The same mistake appears in 80% of them"). How-to / industry analysis → Consequence lead ("Most professionals optimise for visibility. The ones who build durable careers optimise for something else entirely") or direct value statement. |
| Core Sections | Prose sections with optional subheadings. Subheading decision made at Q2 and applied consistently throughout — articles that use subheadings in some sections and not others read as inconsistent. Section length calibrated from translation table. |
| Supporting Examples or Beats | Source Material Block content is distributed here. Each abstract section requires at least one grounding element drawn from Q4 inputs. If Q4 reveals no source material: flag the grounding gap per section — not once globally. Claude never invents statistics, quotes, or case examples for LinkedIn Articles. |
| Source Material Block | Conditional Core. Fires when Q4 confirms specific data, research, personal experience, or case examples exist. Claude organises the user's source material and maps each reference to the section where it will be deployed before writing. This prevents source material from clustering in one section while others run abstract. Drops when Q4 confirms no source material — but absence is flagged as a quality risk. |
| Closing Resolution | Three valid structures by article_type: Thought leadership → Thesis restatement with implication ("If this is true, then the way most teams approach X needs to change"). Personal narrative / how-to → Practical close (summary of steps or single most important action). Industry analysis → Call to reflection ("The question worth sitting with is not whether this affects your industry, but when"). |
| Call-to-Action | Conditional Core. LinkedIn Article CTAs: comment invitation tied to thesis, follow prompt, or link to related content. Hard conversion CTAs ("Book a call," "Buy now") damage the editorial register and signal promotional content. If user's content objective implies a hard conversion → flag and recommend softer editorial CTA instead. |

**Constraints:**
- Thesis-not-topic enforcement: Claude evaluates Q1 before proceeding. Topic statement is returned with request to sharpen into a defensible claim. Generation does not begin without a thesis.
- Source material mapped before generation when Q4 provides references. Source material integrated after the fact produces uneven articles — this is a generation sequence rule.
- LinkedIn editorial register: professional editorial, not social media posting register. What the register constraint prohibits: emoji-heavy formatting, listicle-style bullet saturation, casual contractions used structurally rather than for voice, promotional language signalling advertorial rather than editorial intent. Conversational professional writing is acceptable — it is not a formality constraint.
- No invented professional credentials, case references, statistics, or quotes. Hallucination risk on a professional platform with a credibility-sensitive audience is materially higher than on consumer social platforms.
- Subheading decision from Q2 is applied consistently throughout the article.
