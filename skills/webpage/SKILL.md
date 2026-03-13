# Webpage Generation Skill

**Role:** Generate webpage content from a completed CONTENT_BRIEF.
Intake is already done. Phase 3 subtype questions are already answered.
This skill reads the brief and generates.

**Do not ask intake questions. Do not re-run Phase 1--4. Generate.**

------------------------------------------------------------------------

## Pipeline Position

    /create → intake/SKILL.md → router/SKILL.md → webpage/SKILL.md ← you are here
                                                          ↓
                                                  humanise/SKILL.md

Input: populated CONTENT_BRIEF\
Output: completed webpage content in CONTENT_BRIEF.output_format

------------------------------------------------------------------------

## CONTENT_BRIEF Contract

This skill assumes a fully populated CONTENT_BRIEF produced by the
intake system. Generation must halt if required fields are missing.

**Required fields:** - format - sub_type - topic - angle -
content_goal - target_audience - sector - output_format -
visitor_familiarity

**Optional fields:** - brand_name - brand_description - keyword - tone -
hallucination_risk_components - context_material_used - existing_assets

**visitor_familiarity values and meaning:**

  Value              Meaning
  ------------------ ---------------------------------------------------
  `unaware`          Visitor does not know the product category exists
  `problem-aware`    Knows the problem, not the solution
  `solution-aware`   Knows solutions exist, is evaluating options
  `product-aware`    Knows this specific product, is evaluating fit

`visitor_familiarity` is inferred silently by intake from Phase 2 Q1
Visitor Profile answer. It governs context depth, tone calibration, and
structural emphasis throughout generation.

If any required field is missing → Hard Stop before generation.

------------------------------------------------------------------------

## Pre-Generation Sequence

Before writing any content, run these steps in order:

**Step 1 --- Confirm brief sufficiency.** Run the Silent Layer check
(see below). If the brief fails, fire the Hard Stop before writing a
single word.

**Step 2 --- Validate format.** If `CONTENT_BRIEF.format ≠ "webpage"` →
Hard Stop. This skill only processes webpage format requests.

**Step 3 --- Load sector skill.** Read `sectors/{sector_id}/SKILL.md`
before generating. Sector overlay behaviour applies throughout.

`sector_id` must be resolved from the following canonical mapping. Do
not derive it programmatically --- use this table only:

  CONTENT_BRIEF.sector   sector_id
  ---------------------- ----------------------------------------------------
  IT / SaaS / Tech       `tech`
  Legal / CA / Finance   `ca_legal-finance`
  Healthcare             `healthcare`
  Real Estate            `real-estate`
  Education              `education`
  E-commerce / Retail    `ecommerce-retail`
  Hospitality / Travel   `hospitality-travel`
  Marketing / Agency     `marketing-agency`
  HR / Recruitment       `hr-recruitment`
  Manufacturing / B2B    `manufacturing-b2b`
  Local Services         `local-services`
  general                no sector skill loaded --- proceed without overlay

If `sector_id = general`: skip sector overlay behaviour entirely.
Continue generation using base subtype rules only.

**Step 4 --- Determine humanise pass.** Identify the required
HUMANISE_PASS for this subtype using the table in the Humanise Pass
Assignment section. Valid values: `full` \| `restricted`. Attach
HUMANISE_PASS metadata to the output. The actual transformation is
performed by `skills/humanise/SKILL.md`.

**Step 5 --- Identify hallucination-risk components.** Check
CONTENT_BRIEF.hallucination_risk_components. Apply F.0.3 rules to every
component flagged.

**Step 6 --- Normalise and route to subtype.** Normalise
`CONTENT_BRIEF.sub_type` to lowercase before comparison. Validate
against the whitelist in the Subtype Routing section. Use the validated
sub_type to select the correct generation rules. Only one subtype rule
set may activate.

Normalise `CONTENT_BRIEF.visitor_familiarity` to lowercase before
validation. Valid values after normalisation: `unaware` \|
`problem-aware` \| `solution-aware` \| `product-aware`. If the
normalised value does not match one of these → Hard Stop before
generation. Do not proceed with an unrecognised visitor_familiarity
value — subtype behaviour, context depth, and persuasion tone all depend
on it.

------------------------------------------------------------------------

## Silent Layer --- Brief Sufficiency Check

Run before generating. Never user-facing unless Hard Stop fires.

**Sufficiency standard for web pages:** The brief must contain enough
confirmed information to populate all Core components for the requested
subtype. If Core components cannot be answered from user-provided
material, the brief is insufficient.

  -----------------------------------------------------------------------
  Insufficiency Condition             Hard Stop Response
  ----------------------------------- -----------------------------------
  Core component cannot be populated  Flag the specific missing component
                                      and give three options

  Sales Page: any of the four         Hard Stop --- do not generate sales
  Persuasion Integrity elements       copy without all four
  missing                             

  Privacy/Terms Page: jurisdiction    Hard Stop --- jurisdiction is
  cannot be confirmed                 mandatory

  FAQ Page: fewer than 5 confirmed    Flag --- page may not justify its
  real questions                      own existence
  -----------------------------------------------------------------------

**Hard Stop response format:**

> "Before I start writing, I need to flag something. For a \[subtype\] I
> need \[specific missing material\] --- without that I'd be making it
> up. Three options: 1. Give me \[specific ask\]. 2. I can write what I
> have --- but \[specific component\] will need to be completed by you.
> 3. \[Where applicable\] I can research \[specific element\] --- you'll
> need to review before publishing."

**Sales Page Persuasion Integrity Rule --- Hard Stop elements.**
Generation does not proceed on a Sales Page without all four: 1. Target
customer and their situation 2. Core problem or frustration 3. The offer
--- what it is, what is included, what it costs 4. At least one proof
element --- testimonial, case result, or credential

------------------------------------------------------------------------

## Cross-Subtype Principles

Apply to every webpage subtype without exception.

### Confirmed Zone / Intelligent Extension Zone

-   **Confirmed Zone:** everything the user explicitly provided or
    confirmed. Written with full confidence, no hesitation.
-   **Intelligent Extension Zone:** Claude extends with relevant,
    web-searched and verified supporting material --- industry context,
    community signals, corroborating references.
-   User-provided material from Pre-Phase 1 context extraction is
    confirmed zone --- authoritative by definition.
-   When CONTENT_BRIEF.context_material_used = true: treat all brand
    details as confirmed zone.
-   If something feels like a stretch, it does not go in.

### Evidence Leads on Proof Pages

On Testimonials, Case Study, and Portfolio pages: evidence leads,
attribution is mandatory, and results are never approximated. Every
quote needs minimum name + company. Every metric comes from the user.
Nothing is fabricated.

### Web Search Native

Cowork supports web search natively. Use web search for: community
research, competitor context, industry terminology verification, and any
factual material the user has not provided.

**Confirmed Zone Precedence Rule.** Web search may only populate missing
supporting material. User-provided facts in the Confirmed Zone are never
overridden or contradicted by web search results. If a web search result
conflicts with user-provided information --- the user-provided
information stands. Flag the conflict to the user if it is material.

**Web Search Failure Behaviour.** If web search fails or produces no
credible sources: - Drop the component entirely. - Never fabricate
statistics, claims, or quotes. - Never approximate missing data. - Where
applicable, insert a placeholder:
`[Include your own source here — web search returned no credible results for this component]`.

### Section Ceilings

All web page subtypes have defined section ceilings. Never exceed them.
If content remains after the ceiling --- consolidate, do not add
sections.

  -----------------------------------------------------------------------
  Subtype                             Section Ceiling
  ----------------------------------- -----------------------------------
  Homepage                            7 sections hard maximum

  Landing Page                        7 sections hard maximum

  Sales Page                          9 sections advisory --- 14 sections
                                      absolute consolidation threshold

  Product Page                        7 sections hard maximum

  Services Page                       7 sections hard maximum

  Pricing Page                        7 sections hard maximum

  Industry Page                       7 sections hard maximum

  Testimonials Page                   7 sections hard maximum

  Case Study Page                     7 sections hard maximum

  Portfolio Page                      7 sections hard maximum

  About Page                          7 sections hard maximum

  FAQ Page                            7 sections hard maximum

  Privacy / Terms Page                7 sections hard maximum

  Contact Page                        3--4 sections hard maximum

  404 Page                            3--4 sections hard maximum

  Thank You Page                      3--4 sections hard maximum
  -----------------------------------------------------------------------

**Sales Page exception:** Above 9 sections --- Claude checks whether
additional sections are persuasion-critical (proof, objection handling)
before adding them. Above 14 sections --- consolidate without exception.

### YMYL Sectors

Legal/CA/Finance and Healthcare are YMYL classifications. On all page
types in these sectors: author credentials are mandatory, disclaimers
are auto-added, and anonymous author routes are unavailable. Never
generate YMYL content without these elements in place.

### Hard Stop Rule

Generation must never proceed when the CONTENT_BRIEF is insufficient for
the requested subtype. The validation logic and response format are
defined in the Silent Layer section above.

### Length is an Output, Not a Target

Write to the natural depth required by the subtype and the content
available. Never pad, stretch, or fabricate content to fill space.
Component priority ranking determines what gets dropped when material is
thin.

------------------------------------------------------------------------

## Priority Tier System

Every component in every subtype library is assigned one of four tiers.

  -----------------------------------------------------------------------
  Tier                    Definition              Behaviour
  ----------------------- ----------------------- -----------------------
  **Core**                Must appear in every    Missing info → Hard
                          page of this subtype    Stop or ask before
                                                  generating. Never
                                                  dropped.

  **Conditional Core**    Mandatory only when a   Behaves like Core when
                          specific signal is      signal fires. Absent
                          detected                when signal is not
                                                  present.

  **Supporting**          Appears when            No aggressive asking.
                          information exists or   Subject to selection
                          depth justifies it      cap.

  **Enrichment**          Appears only when page  Dropped first when
                          is long or user         content is thin.
                          provides extra material 
  -----------------------------------------------------------------------

**Supporting Component Selection Cap --- maximums, not targets:**

  -----------------------------------------------------------------------
  Page Depth                          Max Supporting Components
  ----------------------------------- -----------------------------------
  Short utility pages (Contact, 404,  1 maximum
  Thank You)                          

  Standard pages (800--1500 words)    2 maximum

  Conversion pages (1500--2500 words) 3 maximum

  Long-form pages (2500+ words, Sales 4 maximum --- only when sufficient
  Page only)                          material exists
  -----------------------------------------------------------------------

**Conflict Resolution Rule:** When two components serve a similar
visitor need, the lower-tier one is evaluated first. Never stack two
components that produce visible repetition.

------------------------------------------------------------------------

## Sector Overlay Behaviour

Load the relevant sector skill file. The table below defines
auto-behaviours that apply on top of subtype rules for every web page
subtype.

  -----------------------------------------------------------------------
  Sector                              Key Auto-Behaviours
  ----------------------------------- -----------------------------------
  IT / SaaS / Tech                    Technical depth calibrated to
                                      visitor_familiarity. Time-to-value
                                      block fires on Product Pages.
                                      Schema: SoftwareApplication where
                                      applicable.

  Legal / CA / Finance                YMYL. Disclaimer auto-added.
                                      E-E-A-T critical. Author
                                      credentials mandatory on all pages.
                                      Opinion injection banned.

  Healthcare                          YMYL. Medical disclaimer mandatory.
                                      Anonymous author route unavailable.
                                      'When to seek professional
                                      guidance' signal mandatory where
                                      relevant.

  Real Estate                         Location specificity mandatory.
                                      Local SEO fires automatically.

  Education                           Board and curriculum specificity
                                      asked. Outcome-focused framing. Age
                                      and role calibration active.

  E-commerce / Retail                 Product-first framing. Pricing
                                      accuracy critical. Soft CTA rule
                                      applies strictly on non-conversion
                                      pages.

  Hospitality / Travel                Destination specificity. Local SEO
                                      fires. Seasonal framing optional.

  Marketing / Agency                  Results-first. Statistics must be
                                      sourced. 'Results may vary'
                                      qualifier on client metrics.

  HR / Recruitment                    Role specificity. Compliance
                                      framing for legal regions.

  Manufacturing / B2B                 Technical depth. Decision-maker
                                      framing. Jargon permitted.

  Local Services                      Local SEO fires automatically.
                                      City/suburb specificity mandatory.
                                      Trust-first tone. Service radius
                                      framing.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## F.0.3 --- Hallucination Window Rules

Applies system-wide across all webpage subtypes. The hallucination
window opens in the Intelligent Extension Zone --- when Claude populates
components requiring supporting information not provided by the user.

**Risk classification:**

High-risk components --- all three rules apply: - Authority Quote Block
/ Expert Quote Block - Data / Statistics Block - Research Findings
sections - Testimonials and client attributions

Medium-risk components --- Rules 2 and 3 apply: - Industry Context
Block - Community Relevance Block - Competitor or market positioning
claims

When a component entry below carries **\[F.0.3\]**, all applicable rules
govern how that component is populated.

### Rule 1 --- The Source Verification Chain

For any component producing a factual claim sourced from outside the
user's brief: Claude must be able to trace the claim to a specific URL
or named publication retrieved in the **current session's web search**.
If the source cannot be traced to the current session --- the claim does
not go in. Training data recall is not a citable source in this plugin.

### Rule 2 --- The Grey Zone Drop Rule

When web search returns adjacent, outdated, low-quality, or conflicting
results for a section requiring specific factual support --- drop the
section entirely rather than using adjacent material.

Exception: Claude may include the section as a clearly marked
placeholder ---
`[Include your own source here — I found conflicting data on this point]`
--- so the user knows the gap exists.

### Rule 3 --- The Synthetic Authority Signal

Hard stop during generation for these patterns without a traceable
current-session source: - "According to recent research..." without a
traceable URL - "Studies show..." without a named study - "X% of
\[audience\]..." without a traceable source - "Experts agree..." without
named experts - "Industry data suggests..." without a named source - Any
statistic or percentage without a current-session web search citation

### Research Mode Safeguard

Note: `research_mode` is normally set only by blog generation workflows.
Webpage generation does not set this flag. If the flag exists in the
CONTENT_BRIEF (carried over from a repurpose or unusual intake path),
apply the safeguards below. If the flag is absent or false, skip this
block.

If `CONTENT_BRIEF.research_mode = true`: - Treat all non-user facts as
Intelligent Extension Zone content. - Apply hallucination safeguards to
every such claim.

------------------------------------------------------------------------

## CTA Behaviour by content_goal

  -----------------------------------------------------------------------
  content_goal                        CTA Behaviour
  ----------------------------------- -----------------------------------
  educate                             Soft CTA. Suggest related content
                                      or logical next step. No product
                                      push.

  build-authority                     Newsletter, follow, or contact CTA.
                                      Brand mention soft.

  convert                             Product or lead CTA. Hard CTA
                                      permitted on Landing Page and Sales
                                      Page.

  persuade                            Soft-to-hard depending on subtype.
                                      Opinion injection follows subtype
                                      register.

  inform                              Soft or none. Information delivery
                                      is the primary action.

  entertain                           No CTA or very soft brand mention
                                      only.
  -----------------------------------------------------------------------

CTA placement is subtype-specific. Refer to each subtype's component
library for CTA tier, type, and placement rules. Subtype CTA rules
override the table above where they are more specific.

------------------------------------------------------------------------

## Humanise Pass Assignment

Apply the correct HUMANISE_PASS value for the subtype. Attach it as
metadata to the output for processing by `skills/humanise/SKILL.md`. Do
not apply Full Pass standards to Restricted Pass subtypes.

  -----------------------------------------------------------------------
  Subtype                 Humanise Pass           Key Restriction
  ----------------------- ----------------------- -----------------------
  Homepage                Full Pass               None

  Landing Page            Full Pass               None

  Sales Page              Full Pass               None

  Product Page            Full Pass               None

  Services Page           Full Pass               None

  Pricing Page            Restricted Pass         Clarity register
                                                  preserved across all
                                                  sectors --- no override

  Industry Page           Full Pass               None

  Testimonials Page       Restricted Pass         No persuasive framing
                                                  around quotes. Quotes
                                                  speak for themselves.

  Case Study Page         Restricted Pass         No opinion injection.
                                                  No tone softening.
                                                  Analytical register
                                                  preserved.

  Portfolio Page          Sector-dependent (see   ---
                          below)                  

  About Page              Full Pass               None

  FAQ Page                Restricted Pass         Clarity register.
                                                  Answers must be
                                                  precise, not engaging.

  Privacy / Terms Page    Restricted Pass         Legal register
                                                  preserved. No
                                                  narrative, no opinion
                                                  injection, no rhythm
                                                  adjustment.

  Contact Page            Full Pass               None

  404 Page                Full Pass               Tone should be human,
                                                  slightly
                                                  self-deprecating, never
                                                  corporate.

  Thank You Page          Full Pass               None
  -----------------------------------------------------------------------

**Portfolio Page --- sector-dependent rule:** - Full Pass for creative
sectors: design, photography, architecture, creative agencies,
hospitality/experiential. - Restricted Pass for all other sectors:
professional, technical, and B2B contexts.

**Full Pass standards:** - Passive voice: under 10% of sentences -
Readability: Flesch score 60--70 for general audience, 50--60 for
professional/sector content - Paragraphs: maximum 4 sentences for
digital content - Opening line: must not start with 'In', 'The', 'Are
you', or the brand name - Every major section must contain at least one
opinion, observation, or specific insight - Sentence rhythm must vary
--- no 3+ consecutive sentences of the same length

**Restricted Pass --- what applies:** - Passive voice check - Paragraph
length check - Basic rhythm variation - Opening line check

**Restricted Pass --- what does NOT apply:** - Opinion injection ---
banned. Register preserved as written. - Tone softening --- banned.
Professional register preserved. - Narrative rewriting --- banned.
Structure and sequence not altered.

------------------------------------------------------------------------

## G.2 --- SEO Standards

**SEO Activation Rule.** The SEO layer activates only when
`CONTENT_BRIEF.keyword` exists (non-empty) AND the subtype's SEO
Behaviour rule confirms Phase 4 fires for this subtype (see each subtype
section).

**Subtypes where Phase 4 is always skipped:** Homepage (brand terms
handled silently), Pricing Page (brand + pricing terms handled
silently), Testimonials Page, About Page, Privacy/Terms Page, 404 Page,
Thank You Page. Do not activate SEO structure on these subtypes even if
a keyword is present.

When the SEO layer is active: - Keyword in H1, first 100 words, at least
2 H2s, meta title, meta description - Meta title: under 60 characters.
Formula: Primary Keyword + Benefit/Angle + Brand - Meta description:
under 155 characters, includes action verb. Formula: Action verb +
keyword + value prop - LSI keywords: woven naturally --- never forced -
Internal link placeholders: minimum 2 per page - Schema markup
assessment: output as notes at end of content --- schema type is
subtype-specific (see each subtype's SEO Behaviour note)

**Local SEO.** Fires automatically for: Real Estate, Healthcare,
Legal/CA/Finance, Hospitality/Travel, Education, Local Services sectors.
Also fires for Contact Page (local businesses only). City/suburb
specificity mandatory when local SEO fires.

------------------------------------------------------------------------

## G.3 --- Banned Phrases

**Category 1 --- Generic AI-isms:** In today's digital/fast-paced
landscape, In the modern era, In the ever-evolving world of, It's
important to remember that, It's important to note that, Unlock the
secrets, Unleash your potential, Harness the power of, A testament to,
Dive deep into, Delve into, At its core, At the end of the day,
Comprehensive guide, Think of it as

**Category 2 --- Forbidden Transitions:** Furthermore, Moreover, In
addition, Consequently, Additionally, Subsequently, Accordingly, Indeed,
Not only...but also, In conclusion, To summarise, It goes without
saying, Last but not least

**Category 3 --- Lazy Adjectives:** Revolutionary, Game-changing,
Cutting-edge, Robust, Seamless, Synergy, Pivotal, Transformative,
Innovative, Groundbreaking, Dynamic, Powerful, Vibrant, Crucial role,
Ever-evolving, Nuanced approach

**Category 4 --- Corporate Verbs:** Leverage (use: use), Facilitate
(use: help), Utilize (use: use), Endeavour, Underscore (use:
show/prove), Optimize (use: improve), Revolutionize, Empower, Foster,
Navigate (metaphorical), Circle back, Deep dive, Move the needle,
Streamline

------------------------------------------------------------------------

## Pre-Output Validation

Scan generated content for placeholder markers before producing final
output.

If a placeholder remains: - Notify the user that the section contains an
unresolved placeholder. - Do not output that section as final content.

Also confirm before finalising any webpage: - \[ \] Format validated:
CONTENT_BRIEF.format = "webpage" - \[ \] Sector skill loaded and sector
behaviours applied - \[ \] Correct humanise pass attached as metadata -
\[ \] No banned phrases from G.3 present - \[ \] All Core components
present for this subtype - \[ \] All high-risk / medium-risk components
verified against F.0.3 rules - \[ \] No statistics, quotes, or
testimonials without a confirmed user-provided or current-session web
search source - \[ \] Section ceiling for this subtype not exceeded - \[
\] SEO elements present only if SEO layer activated: meta title, meta
description, internal link placeholders, schema notes - \[ \] Output
format matches CONTENT_BRIEF.output_format - \[ \] YMYL sectors: author
credentials present and disclaimer added - \[ \] Pricing Page: no soft
framing applied --- clarity register preserved - \[ \] Case Study /
Testimonials Page: no persuasive language anywhere, all attributions
confirmed

------------------------------------------------------------------------

## Output Format

Respect CONTENT_BRIEF.output_format:

  -----------------------------------------------------------------------
  Value                               Behaviour
  ----------------------------------- -----------------------------------
  `markdown`                          Default. Structured markdown with
                                      headers, paragraphs, and lists
                                      where appropriate.

  `html`                              Semantic HTML. H1/H2/H3 hierarchy.
                                      No inline styles.

  `plain-text`                        No markdown syntax. Headers as
                                      plain capitalised lines.

  `cms-ready`                         Markdown with CMS-friendly heading
                                      structure and placeholder
                                      image/asset labels where
                                      applicable.
  -----------------------------------------------------------------------

After the main content, output as a separate block: - Meta title (when
SEO layer active) - Meta description (when SEO layer active) - Schema
markup notes (when SEO layer active, schema type noted per subtype) -
Internal link placeholder list (when SEO layer active) - Legal
disclaimer note (Privacy/Terms Page only --- as an external system note,
never embedded in content)

------------------------------------------------------------------------

## Subtype Routing

**Valid webpage sub_type values.** Normalise to lowercase before
comparison. If the normalised value does not match one of these values →
Hard Stop before generation:

-   `homepage`
-   `landing-page`
-   `sales-page`
-   `product-page`
-   `services-page`
-   `pricing-page`
-   `industry-page`
-   `testimonials-page`
-   `case-study-page`
-   `portfolio-page`
-   `about-page`
-   `faq-page`
-   `privacy-terms-page`
-   `contact-page`
-   `404-page`
-   `thank-you-page`

**Subtype execution guard.** Only the rules belonging to the selected
subtype are active. All other subtype rule sections must be ignored.

  sub_type               Family                             Active Section
  ---------------------- ---------------------------------- ----------------
  `homepage`             Family 1 --- Orientation           I.A.1
  `landing-page`         Family 2 --- Conversion            I.B.1
  `sales-page`           Family 2 --- Conversion            I.B.2
  `product-page`         Family 3 --- Evaluation            I.C.1
  `services-page`        Family 3 --- Evaluation            I.C.2
  `pricing-page`         Family 3 --- Evaluation            I.C.3
  `industry-page`        Family 3 --- Evaluation            I.C.4
  `testimonials-page`    Family 4 --- Proof                 I.D.1
  `case-study-page`      Family 4 --- Proof                 I.D.2
  `portfolio-page`       Family 4 --- Proof                 I.D.3
  `about-page`           Family 5 --- Trust & Information   I.E.1
  `faq-page`             Family 5 --- Trust & Information   I.E.2
  `privacy-terms-page`   Family 5 --- Trust & Information   I.E.3
  `contact-page`         Family 6 --- Utility               I.F.1
  `404-page`             Family 6 --- Utility               I.F.2
  `thank-you-page`       Family 6 --- Utility               I.F.3

------------------------------------------------------------------------

## Subtype Generation Rules

------------------------------------------------------------------------

### I.A --- Family 1: Orientation Pages

------------------------------------------------------------------------

### I.A.1 --- Homepage

**Core Rule:** Homepage is the only page type with navigation
architecture as a structural requirement. Every other page has one job.
Homepage has four simultaneously: orient, qualify, route, and convert.
The hero block answers what you do and who it is for --- in one line.
Not a tagline. Not a slogan. A plain answer.

**Humanise Pass:** Full Pass

**Structural Order --- Locked:** 1. Hero 2. Social Proof Snapshot 3.
Product / Service Overview 4. Navigation Pathways 5. Supporting
explanation or differentiator 6. Secondary trust signal or CTA
reinforcement 7. Final CTA (if section ceiling not yet hit)

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → What a first-time visitor must understand in the first
5 seconds → drives hero block - Q2 → Who the different visitor types are
and where each should go → drives navigation pathway block - Q3 →
Strongest trust signal → drives social proof snapshot - Q4 → Main
products or services to discover from this page → drives product/service
overview layer - Q5 → Brand differentiation question (I.6) → drives
brand differentiation block

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Hero Block        Core              Always fires      Value
                                                        proposition +
                                                        primary CTA.
                                                        Headline answers
                                                        what you do and
                                                        who it is for in
                                                        one line. Not a
                                                        tagline.

  Social Proof      Core              Always fires      Logos, numbers,
  Snapshot                                              one-line
                                                        testimonials.
                                                        Condensed rapid
                                                        trust. Appears
                                                        within first 3
                                                        sections. Not
                                                        full
                                                        testimonials.
                                                        **\[F.0.3\]**

  Product / Service Core              Fires from Phase  What the company
  Overview Layer                      3 Q4              actually sells.
                                                        Hard stop if this
                                                        cannot be
                                                        answered --- a
                                                        homepage without
                                                        substance cannot
                                                        be written.

  Navigation        Conditional Core  Fires when Phase  1 visitor type →
  Pathway Block                       3 Q2 reveals 2+   block does not
                                      distinct visitor  fire. 2--3
                                      types             visitor types →
                                                        block fires with
                                                        distinct routes.
                                                        4+ visitor types
                                                        → consolidate to
                                                        3 maximum.

  CTA Architecture  Core              Always fires      One primary CTA
                                                        in hero. One
                                                        secondary CTA
                                                        lower on page.
                                                        Maximum two
                                                        distinct CTA
                                                        types. Third CTA
                                                        type is a hard
                                                        stop ---
                                                        consolidate
                                                        first.

  Brand             Supporting        Fires from Phase  What makes this
  Differentiation                     3 brand           company
  Block                               differentiation   different. Never
                                      question          fabricated.
  -----------------------------------------------------------------------

**Constraint Layer:** - 7 sections hard maximum - Hero block is always
section one - Social proof snapshot within first three sections -
Navigation pathway block fires only when two or more distinct visitor
types confirmed - Maximum two distinct CTA types on the entire page

**SEO Behaviour:** Phase 4 keyword question skipped. Brand name is
primary keyword by default. Meta title: Brand + core category. Meta
description: value proposition summary. Applied silently --- no Phase 4
question asked.

------------------------------------------------------------------------

### I.B --- Family 2: Conversion Pages

Landing Page and Sales Page share persuasion-first architecture. The
visitor arrives in evaluation mode --- deciding, not discovering. Shared
logic: problem-first structure, objection handling inline, CTA placed
where commitment is most likely.

------------------------------------------------------------------------

### I.B.1 --- Landing Page

**Core Rule:** Shorter, faster, single-offer focused. Low-to-medium
commitment decision. Structure is compressed. One screen of context, one
action. Urgency fires only when confirmed real --- never fabricated or
implied.

**Humanise Pass:** Full Pass

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Single action this page exists to drive → locks the
one CTA - Q2 → The specific offer → drives benefit stack and headline -
Q3 → Top 3 reasons someone would hesitate → drives objection handling
block - Q4 → Where traffic is coming from → calibrates tone and assumed
familiarity - Q5 → Urgency or incentive → fires or drops urgency block -
Q6 → Brand differentiation question (I.6) → drives brand differentiation
block

**Enrichment Component Library:**

  -------------------------------------------------------------------------
  Component           Tier              Activation Signal Rules
  ------------------- ----------------- ----------------- -----------------
  Single-Goal         Core              Always fires      One CTA type
  Constraint                                              only. Zero
                                                          navigation links.
                                                          If user provides
                                                          multiple goals
                                                          --- flag and
                                                          force a choice
                                                          before
                                                          generating.

  Problem-Agitation   Core              Always fires      Visitor pain
  Block                                                   stated in their
                                                          language before
                                                          the offer
                                                          appears.

  Benefit Stack       Core              Always fires      Outcomes listed,
                                                          not features.
                                                          3--5 benefits
                                                          maximum before
                                                          first CTA
                                                          appears.

  Objection Handling  Core              Always fires      3--5 specific
  Block                                                   objections
                                                          answered inline.
                                                          Not in FAQ format
                                                          at bottom.

  Traffic Source      Conditional Core  Fires from Phase  Cold ad traffic →
  Calibration                           3 Q4              more context.
                                                          Warm email
                                                          traffic → less.
                                                          Applied to
                                                          opening tone and
                                                          context depth.

  Urgency / Incentive Conditional Core  Fires ONLY when   Never fabricated
  Block                                 Phase 3 Q5        or implied. If no
                                        confirms a real   urgency exists
                                        urgency signal    --- block dropped
                                                          entirely.

  Brand               Supporting        Fires from Phase  Never fabricated.
  Differentiation                       3 brand           
  Block                                 differentiation   
                                        question          
  -------------------------------------------------------------------------

**Constraint Layer:** - 7 sections hard maximum - Single CTA type ---
hard rule, no exceptions - Urgency block never fabricated --- dropped if
not confirmed - No navigation links --- hard rule - Traffic source must
be known before generating opening tone

**SEO Behaviour:** Phase 4 keyword question fires normally.

------------------------------------------------------------------------

### I.B.2 --- Sales Page

**Core Rule:** Long-form persuasion. Higher-commitment decision. Every
persuasion layer must be present and in sequence. Length is justified by
decision weight --- not by padding.

**Humanise Pass:** Full Pass

**Persuasion Integrity Rule --- Hard Stop.** Generation does not proceed
without all four: 1. Target customer / situation 2. Core problem or
frustration 3. The offer --- what it is, what is included, what it costs
4. At least one proof element --- testimonial, case result, or
credential

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → The person this is written for, their situation, their
frustration, what they want instead → drives pain amplification - Q2 →
Full offer: price, what's included, bonuses, what the buyer receives →
drives price anchoring and offer stack - Q3 → Strongest proof:
testimonials, case results, credentials → drives social proof
placement - Q4 → Guarantee: what it is exactly → drives guarantee block
or flags absence - Q5 → Brand differentiation question (I.6) → drives
brand differentiation block

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Pain              Core              Always fires      Longer and more
  Amplification                                         emotionally
  Section                                               resonant than
                                                        Landing Page
                                                        problem block.
                                                        Reader must feel
                                                        the cost of not
                                                        acting before the
                                                        offer appears.

  Transformation    Core              Always fires      Before state vs
  Promise                                               after state.
                                                        Explicit, not
                                                        implied.

  Full Offer        Core              Always fires      Price, what's
  Breakdown                                             included,
                                                        bonuses, delivery
                                                        method. Never
                                                        omitted unless
                                                        user explicitly
                                                        withholds.

  Price Anchoring   Conditional Core  Fires when price  Justifies the
  Block                               is above impulse  number before or
                                      threshold         immediately after
                                                        revealing it.

  Guarantee Block   Conditional Core  Fires when Phase  Absence flagged
                                      3 Q4 confirms     in orientation
                                      guarantee exists  statement if
                                                        missing.
                                                        High-ticket page
                                                        without guarantee
                                                        --- flag to user
                                                        before
                                                        generating.

  Multiple CTA      Core              Always fires      Minimum 3 CTA
  Placements                                            placements: after
                                                        transformation
                                                        promise, after
                                                        proof section,
                                                        after guarantee.
                                                        Never fewer than
                                                        3.

  Comparison /      Supporting        Fires only when   Never fabricated.
  Positioning Block                   user confirms     
                                      they want to      
                                      position against  
                                      alternatives      

  Objection         Core              Always fires      Inline, not in
  Handling Block                                        FAQ at bottom.
                                                        Objections appear
                                                        where they
                                                        naturally arise
                                                        in reading flow.
  -----------------------------------------------------------------------

**Constraint Layer:** - 9 sections advisory --- 14 sections absolute
consolidation threshold - Persuasion sequence is non-negotiable ---
sections cannot be reordered without flagging consequences to the user -
Minimum 3 CTA placements --- hard rule - Comparison block never
fabricated

**SEO Behaviour:** Phase 4 keyword question fires for SEO-targeted sales
pages. Skipped when page is purely paid traffic destination with no
organic intent.

------------------------------------------------------------------------

### I.C --- Family 3: Evaluation Pages

Product Page, Services Page, Pricing Page, Industry Page. Shared job:
help a visitor who already knows they have a problem decide whether this
specific solution fits. Visitor is in evaluation mode. Shared logic:
specificity over generality, outcomes over features, clarity over
persuasion.

------------------------------------------------------------------------

### I.C.1 --- Product Page

**Humanise Pass:** Full Pass

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → What the product does and the specific outcome a user
gets → drives feature-benefit matrix - Q2 → The 3--5 features that
matter most to the buyer → populates feature list - Q3 → Integrations
with other tools or platforms → drives integration block - Q4 → Whether
the product is technical, requiring specs or developer documentation →
fires or drops technical specs block - Q5 → Brand differentiation
question (I.6) → drives brand differentiation block

**Enrichment Component Library:**

  ---------------------------------------------------------------------------
  Component         Tier              Activation Signal     Rules
  ----------------- ----------------- --------------------- -----------------
  Feature-Benefit   Core              Always fires          Each feature
  Matrix                                                    paired with its
                                                            outcome. Never a
                                                            feature list
                                                            without outcomes.
                                                            Hard stop if user
                                                            cannot describe
                                                            product outcomes.

  Screenshot / Demo Conditional Core  Fires when product is If user cannot
  Gallery                             visual or             provide assets
                                      interface-driven.     --- describe
                                      Drops for non-visual  structurally as
                                      products              placeholder.
                                                            Never fabricated.

  Integration       Supporting        Fires when Phase 3 Q3 **\[F.0.3\]** for
  Ecosystem Block                     confirms integrations third-party
                                      are a purchase signal claims.

  Technical Specs   Conditional Core  Fires only for        Never fires
  Block                               hardware, developer   unless sector or
                                      tools, or technical   audience confirms
                                      buyer audiences ---   technical nature.
                                      detected from sector  
                                      and                   
                                      visitor_familiarity   

  Time-to-Value     Supporting        Fires for IT/SaaS     Never asked as a
  Block                               sector only --- via   Phase 3 question.
                                      sector overlay        

  Ideal Fit Block   Supporting        Fires when available  Who this product
                                      content supports it   is for and who it
                                                            isn't. Both sides
                                                            mandatory when it
                                                            fires.
  ---------------------------------------------------------------------------

**Constraint Layer:** - 7 sections hard maximum - Feature-benefit matrix
is Core --- hard stop if user cannot describe product outcomes -
Technical specs block never fires unless sector is IT/SaaS/Tech or
visitor_familiarity = product-aware with technical signals -
Screenshot/demo gallery described structurally if user cannot provide
assets

**SEO Behaviour:** Phase 4 fires. Schema: Product or SoftwareApplication
depending on sector.

------------------------------------------------------------------------

### I.C.2 --- Services Page

**Humanise Pass:** Full Pass

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Each service: what it is, who it's for, what the
client gets → drives service breakdown cards - Q2 → Defined process or
methodology → drives process block - Q3 → Ideal client and type of
client/project not taken on → drives ideal client profile - Q4 → Pricing
approach on this page: ranges, starting figures, or consultation only →
drives pricing structure hint - Q5 → Brand differentiation question
(I.6) → drives brand differentiation block

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Service Breakdown Core              Always fires      One card per
  Cards                                                 service. Each
                                                        card: what it is,
                                                        who it's for,
                                                        what the
                                                        deliverable is.
                                                        Minimum 2
                                                        sentences per
                                                        card. Never a
                                                        name without a
                                                        description.

  Process /         Conditional Core  Fires when Phase  Dropped if user
  Methodology Block                   3 Q2 confirms a   has no
                                      defined process   methodology ---
                                      exists            never fabricated.

  Ideal Client      Supporting        Fires when Phase  Who you work with
  Profile Block                       3 Q3 confirms     and who you
                                      distinct client   don't. Both sides
                                      profile           mandatory when it
                                                        fires.

  Pricing Structure Conditional Core  Three states:     All three are
  Hint                                show ranges /     valid. Claude
                                      show starting     never defaults to
                                      price /           hiding pricing
                                      consultation only without the user
                                                        confirming that
                                                        preference.

  Brand             Supporting        Fires from Phase  Never fabricated.
  Differentiation                     3 brand           
  Block                               differentiation   
                                      question          
  -----------------------------------------------------------------------

**Constraint Layer:** - 7 sections hard maximum - Service breakdown
cards are Core - Methodology block dropped if user has no defined
process --- never fabricated - Pricing structure decision belongs to the
user --- Claude presents options, not a default

**SEO Behaviour:** Phase 4 fires. Local SEO fires automatically for
relevant sectors.

------------------------------------------------------------------------

### I.C.3 --- Pricing Page

**Core Rule:** Visitors scan pricing pages --- they do not read them
narratively. Clarity and scanability are the primary design principles.
Warmth may appear in microcopy only.

**Humanise Pass:** Restricted Pass --- all sectors, no override.

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Pricing tiers: names, prices, what's included → drives
plan comparison table - Q2 → Monthly and annual billing, discount for
annual → drives toggle logic - Q3 → Which plan to highlight as
recommended or most popular → drives visual highlight - Q4 → Questions
buyers usually ask about pricing before committing → drives billing FAQ
block - Q5 → Free trial, free plan, or refund policy → drives risk
reduction block

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Plan Comparison   Core              Always fires      Tiers side by
  Table                                                 side with feature
                                                        availability.
                                                        Hard stop if user
                                                        cannot provide
                                                        plan details. A
                                                        pricing page
                                                        without a
                                                        comparison table
                                                        is a blank page.

  Most Popular Plan Conditional Core  Fires when Phase  If user cannot
  Highlight                           3 Q3 confirms a   name one ---
                                      preferred plan    dropped, never
                                                        guessed.

  Annual / Monthly  Conditional Core  Fires when Phase  Described
  Toggle Logic                        3 Q2 confirms     structurally ---
                                      both billing      actual toggle is
                                      options exist     a UI/development
                                                        component.

  Free Trial /      Conditional Core  Fires from Phase  Risk reduction
  Refund Policy                       3 Q5              signal. Dropped
  Block                                                 if neither exists
                                                        --- never implied
                                                        when absent.

  Billing FAQ Block Supporting        Fires when        Answers must be
                                      pricing           direct.
                                      complexity is     
                                      high: 3+ plans,   
                                      usage-based       
                                      pricing, or       
                                      enterprise tier   
                                      present           
  -----------------------------------------------------------------------

**Constraint Layer:** - 7 sections hard maximum - Plan comparison table
is Core --- no exceptions - Most popular highlight must be
user-confirmed --- never guessed - Phase 4 keyword question skipped ---
brand + pricing terms handled silently

**SEO Behaviour:** Phase 4 skipped. Brand + pricing terms handled
silently.

------------------------------------------------------------------------

### I.C.4 --- Industry Page

**Core Rule:** The page must feel written for that industry --- not
adapted from a generic template. Industry-specific terminology, pain
points, and proof are mandatory. A page that could apply to any industry
has failed its brief.

**Humanise Pass:** Full Pass

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Which industry and the specific problem the product
solves for it → drives pain points and use case scenarios - Q2 → Clients
or results specifically from this industry → drives industry-specific
social proof - Q3 → Language this industry uses: specific terms, job
titles, acronyms → drives terminology calibration - Q4 → Features or
capabilities that matter especially for this industry → drives industry
feature mapping - Q5 → Brand differentiation question (I.6) → drives
brand differentiation block

**Enrichment Component Library:**

  ---------------------------------------------------------------------------
  Component           Tier              Activation Signal Rules
  ------------------- ----------------- ----------------- -------------------
  Industry-Specific   Core              Always fires      Named for that
  Pain Points                                             vertical, not
                                                          generic. Hard stop
                                                          if user cannot
                                                          articulate
                                                          industry-specific
                                                          pain --- generic
                                                          framing defeats the
                                                          page's purpose.

  Use Case Scenarios  Core              Always fires      2--3 specific
                                                          situations where
                                                          the product applies
                                                          in that industry.
                                                          Named roles, named
                                                          problems.

  Industry            Core              Applied           Job titles,
  Terminology                           throughout        acronyms,
  Calibration                           writing           regulatory
                                                          references where
                                                          relevant. Never a
                                                          separate section
                                                          --- woven into the
                                                          writing.

  Industry-Specific   Conditional Core  Fires from Phase  Industry-matched
  Social Proof Block                    3 Q2              clients or results
                                                          only.
                                                          Cross-industry
                                                          proof is actively
                                                          counterproductive
                                                          on an industry
                                                          page. **\[F.0.3\]**

  Industry Feature    Supporting        Fires from Phase  Which specific
  Mapping                               3 Q4              features matter for
                                                          this industry.

  Brand               Supporting        Fires from Phase  Why this product is
  Differentiation                       3 brand           especially suited
  Block                                 differentiation   to this industry
                                        question          versus a generic
                                                          alternative.
  ---------------------------------------------------------------------------

**Constraint Layer:** - 7 sections hard maximum - Industry pain points
are Core - Social proof must be industry-matched --- general
testimonials dropped or withheld - Terminology calibration applied
throughout --- never a standalone section

**SEO Behaviour:** Phase 4 fires. Keyword typically follows pattern:
\[product\] for \[industry\]. If user provides a weak keyword --- Claude
suggests this pattern.

------------------------------------------------------------------------

### I.D --- Family 4: Proof Pages

Testimonials Page, Case Study Page, Portfolio Page. Shared job: let
evidence do the persuading. Shared logic: evidence leads, attribution is
mandatory, results are stated exactly as provided --- never
approximated, never inflated.

------------------------------------------------------------------------

### I.D.1 --- Testimonials Page

**Humanise Pass:** Restricted Pass

**Core Fabrication Rule:** Every quote needs minimum name + company.
Name-only is below the attribution floor --- that quote is dropped.
Never write persuasive framing around quotes. Quotes speak for
themselves. Introductory sentences are factual, not promotional.

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → The testimonials to feature, with name, role, and
company → core content - Q2 → Video testimonials or text only → fires or
drops video block - Q3 → Testimonials from different industries or use
cases, whether to group → drives filter or keeps flat - Q4 → Whether
logos, photos, or company names can be included → drives credibility
depth block

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Quote and         Core              Always fires      Name + role +
  Attribution Block                                     company + photo
                                                        when available.
                                                        Attribution
                                                        floor: name +
                                                        company minimum.
                                                        Name-only → quote
                                                        dropped.
                                                        **\[F.0.3\]**

  Video Testimonial Conditional Core  Fires when Phase  Structural
  Block                               3 Q2 confirms     description only.
                                      video exists      Never fabricated.

  Review Source     Supporting        Fires when        G2, Trustpilot,
  Aggregation Block                   confirmed         Google ratings
                                      third-party       --- attributed to
                                      review sources    named platform.
                                      exist             Never 'customers
                                                        say' with no
                                                        source.
                                                        **\[F.0.3\]**

  Industry / Use    Supporting        Fires when Phase  Minimum 2 items
  Case Filter Logic                   3 Q3 confirms     per cluster --- a
                                      testimonials span single-item
                                      multiple segments cluster is
                                                        flattened into
                                                        the main list.

  Testimonial       Supporting        Fires from Phase  Logos, photos,
  Credibility Block                   3 Q4              company names.
                                                        Dropped cleanly
                                                        if unavailable.
  -----------------------------------------------------------------------

**Constraint Layer:** - 7 sections hard maximum - Every quote needs
minimum name + company - No persuasive framing around quotes -
Introductory sentences are factual only

**SEO Behaviour:** Phase 4 skipped. Brand + review terms handled
silently.

------------------------------------------------------------------------

### I.D.2 --- Case Study Page

**Core Rule:** Mirrors blog Case Study (F.7) in extraction logic and
tone. Shorter output for web format. Same confirmed-zone rules apply ---
every specific detail, metric, and client name comes from the user.
Approximate numbers acceptable. Vague descriptors never acceptable.

**Humanise Pass:** Restricted Pass

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Full story: who was the client, what was the problem,
what was done, what were the results → primary content extraction - Q2 →
Whether client can be named or must be anonymised → disclosure rule - Q3
→ Whether a downloadable PDF version is wanted → fires PDF download
CTA - Q4 → How long the project took and what the major steps were →
drives implementation timeline block

**Enrichment Component Library:**

  ------------------------------------------------------------------------------------
  Component                    Tier              Activation Signal Rules
  ---------------------------- ----------------- ----------------- -------------------
  Metrics Highlight Bar        Core              Always fires      Key result numbers
                                                                   surfaced before
                                                                   body content. If no
                                                                   metrics exist ---
                                                                   flag in orientation
                                                                   statement, proceed
                                                                   as
                                                                   testimonial-style
                                                                   narrative with
                                                                   explicit note.

  Client Profile Snapshot      Core              Always fires      Who the client is,
                                                                   their size, their
                                                                   challenge --- in 2
                                                                   lines maximum.
                                                                   Placed before
                                                                   narrative body.

  Challenge-Solution-Results   Core              Always fires      Same three-part
  Structure                                                        logic as blog F.7
                                                                   but compressed for
                                                                   web format.

  Implementation Timeline      Supporting        Fires when Phase  Dropped if absent
  Block                                          3 Q4 confirms     --- never estimated
                                                 timeline and      or fabricated.
                                                 steps are         
                                                 available         

  PDF Download CTA             Supporting        Fires from Phase  Soft CTA only.
                                                 3 Q3 when         
                                                 long-form version 
                                                 exists            
  ------------------------------------------------------------------------------------

**Constraint Layer:** - Every metric, result, and client detail comes
from user --- confirmed zone rule identical to blog F.7 - Approximate
numbers acceptable --- vague descriptors never acceptable - Attribution
confirmed before any client name appears - 7 sections hard maximum

**SEO Behaviour:** Phase 4 fires. Keyword typically \[service\] case
study or \[industry\] results.

------------------------------------------------------------------------

### I.D.3 --- Portfolio Page

**Humanise Pass:** Sector-dependent. Full Pass for creative sectors
(design, photography, architecture, creative agencies,
hospitality/experiential). Restricted Pass for all other sectors.

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → 3--5 pieces of work to feature: brief, what was done,
result → drives work sample cards - Q2 → Whether clients can be named or
work must be anonymised → disclosure rule - Q3 → Whether work should be
filterable by category, industry, or service type → drives filter
logic - Q4 → Measurable results for any projects → drives results
metrics overlay

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Work Sample Cards Core              Always fires      Project name +
                                                        category + brief
                                                        outcome per card.
                                                        Minimum viable
                                                        card: name +
                                                        one-line outcome.
                                                        Cards without
                                                        outcome are
                                                        incomplete.

  Project Detail    Conditional Core  Fires for         Brief, process,
  Structure                           featured work     result. Remaining
                                      only --- maximum  cards stay at
                                      3 featured        summary level.
                                      projects          

  Filter / Category Supporting        Fires when Phase  Described
  Logic                               3 Q3 confirms     structurally ---
                                      work spans        actual filter is
                                      multiple          a UI/development
                                      categories        component.

  Client Disclosure Core              Always fires      Named or
                                                        anonymised.
                                                        Attribution
                                                        floor: named
                                                        where possible.
                                                        Same rules as
                                                        F.7.

  Results Metrics   Supporting        Fires when Phase  Appears on the
  Overlay                             3 Q4 confirms     card itself. When
                                      measurable        absent ---
                                      results exist     dropped cleanly.
                                                        Never substituted
                                                        with qualitative
                                                        descriptors.
                                                        **\[F.0.3\]**
  -----------------------------------------------------------------------

**Constraint Layer:** - Work sample cards are Core - Maximum 3 featured
projects with full detail - Results metrics: on card when present,
dropped when absent - 7 sections hard maximum

**SEO Behaviour:** Phase 4 fires for agencies and studios. Skipped for
individual freelancer portfolios where brand-name search dominates.

------------------------------------------------------------------------

### I.E --- Family 5: Trust and Information Pages

About Page, FAQ Page, Privacy/Terms Page. Shared register: inform and
build credibility without directly selling. Visitor is in verification
mode. Shared logic: substance over persuasion, clarity over narrative
(except About Page where story is appropriate).

------------------------------------------------------------------------

### I.E.1 --- About Page

**Core Rule:** No product selling on the About Page. Product mentions
are incidental context only. Story and values are the point.

**Humanise Pass:** Full Pass --- narrative and voice are the point. This
is the one page in Family 5 where story is structurally appropriate.

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Founding story: why the company started, what problem
it was trying to solve → drives origin narrative - Q2 → Whether page
should feature specific people or stay brand-level → drives team block
or drops it - Q3 → What someone should do after reading this page →
determines CTA type - Q4 → Milestones, achievements, or recognisable
clients → drives milestones and credibility block

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Mission and       Core              Always fires      Without this an
  Values Block                                          About Page has no
                                                        anchor. If user
                                                        cannot articulate
                                                        values --- Claude
                                                        asks once more
                                                        with different
                                                        framing before
                                                        hard-stopping.

  Origin Narrative  Conditional Core  Fires from Phase  Human story, not
  Block                               3 Q1              corporate
                                                        timeline. If user
                                                        cannot provide
                                                        founding story
                                                        --- page proceeds
                                                        on mission and
                                                        values only.

  Team / Founder    Conditional Core  Fires when Phase  Individuals
  Block                               3 Q2 confirms     presented with
                                      named individuals role and
                                                        relevance, not
                                                        just name and
                                                        title. Drops
                                                        cleanly if user
                                                        does not want to
                                                        feature people.

  Milestones and    Supporting        Fires from Phase  Achievements,
  Credibility Block                   3 Q4              recognisable
                                                        clients, awards,
                                                        growth signals.
                                                        **\[F.0.3\]** for
                                                        third-party
                                                        claims.

  Soft CTA Block    Supporting        Fires from Phase  Type determined
                                      3 Q3              by user answer.
                                                        Never a hard
                                                        conversion push.
  -----------------------------------------------------------------------

**Constraint Layer:** - Mission and values is Core --- no exceptions -
Team block: individuals presented with role and relevance - No product
selling on About Page - 7 sections hard maximum

**SEO Behaviour:** Phase 4 skipped. About pages rank on brand terms.
Handled silently.

------------------------------------------------------------------------

### I.E.2 --- FAQ Page

**Core Rule:** Every question must be real --- confirmed by the user as
something customers actually ask. Claude never invents FAQ questions.
Fewer than 5 confirmed questions --- flag that the page may not justify
its own existence before generating.

**Humanise Pass:** Restricted Pass --- clarity register. Answers must be
precise, not engaging.

**Answer Structure:** Direct answer first, elaboration after. Every
answer: one-sentence answer, then explanation if needed. Answers that
open with context before answering are a failure mode.

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Questions customers ask most often, before and after
buying → core content source - Q2 → Whether to group by theme or keep a
flat list → drives cluster organisation - Q3 → Goal of the page: SEO,
customer support reduction, or both → calibrates question phrasing and
answer depth - Q4 → What someone should do if they don't find their
answer → drives support escalation block

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Question Cluster  Conditional Core  Fires when Phase  Minimum 2
  Organisation                        3 Q2 confirms     questions per
                                      grouping          cluster. A
                                                        cluster with one
                                                        question is
                                                        flattened.

  Answer Depth      Core              Always fires      Direct answer
  Calibration                                           first,
                                                        elaboration
                                                        after. Applied to
                                                        every answer
                                                        without
                                                        exception.

  SEO-Targeted      Core              Applied silently  Questions phrased
  Question Framing                    to question       how people
                                      headers only      actually search.
                                                        Never changes
                                                        substance of
                                                        answers. PAA
                                                        integration fires
                                                        automatically.

  Support           Supporting        Fires from Phase  What happens when
  Escalation Block                    3 Q4              a visitor doesn't
                                                        find their
                                                        answer. Dropped
                                                        if user has no
                                                        escalation path
                                                        --- orientation
                                                        statement notes
                                                        the absence.

  Link-Out Block    Supporting        Fires when user   Internal links
                                      confirms relevant from answers to
                                      deeper pages      deeper pages.
                                      exist             
  -----------------------------------------------------------------------

**Constraint Layer:** - Every question must be confirmed real --- never
invented - Fewer than 5 questions → flag before generating - 7 sections
hard maximum

**SEO Behaviour:** Phase 4 fires. FAQ pages are strong SEO assets ---
question-format content ranks for long-tail and voice search. PAA
integration fires automatically.

------------------------------------------------------------------------

### I.E.3 --- Privacy Policy / Terms Page

**Core Rule:** Legal register preserved throughout. No narrative. No
opinion injection. No rhythm adjustment.

**Humanise Pass:** Restricted Pass --- legal register, no override.

**Jurisdiction Hard Stop:** If jurisdiction cannot be confirmed from
Phase 3 Q1 --- generation does not proceed. Jurisdiction is a hard stop
for this subtype.

**Legal Disclaimer Rule:** The legal disclaimer is delivered as an
external system note only. It is never embedded inside the generated
page content. After generating the page, Claude outputs as a separate
block: "Note: This page was generated as a structural template. It must
be reviewed and approved by a qualified legal professional before
publishing. Claude is not a lawyer. This content does not constitute
legal advice."

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Jurisdictions or regulations: GDPR, CCPA, Indian IT
Act, or others → Hard Stop if cannot be confirmed - Q2 → Data
collection, cookies, third-party tracking, sharing → drives data
collection section - Q3 → Plain-language summary alongside legal text,
or legal text only → fires or drops plain language toggle

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Legal Section     Core              Always fires      Data collection,
  Structure                                             usage, cookies,
                                                        third parties,
                                                        user rights,
                                                        contact
                                                        information. All
                                                        six present
                                                        unless
                                                        jurisdiction
                                                        rules make a
                                                        section
                                                        inapplicable ---
                                                        state the reason
                                                        explicitly.

  Jurisdiction      Core              Hard stop if      Required before
  Declaration Block                   cannot be         any other
                                      confirmed         section.

  Last Updated Date Core              Always fires      Mandatory, placed
                                                        at top. No
                                                        exceptions.

  Cookie and        Conditional Core  Fires when Phase  Never assumed to
  Tracking                            3 Q2 confirms     be absent ---
  Disclosure                          data collection   confirm
                                      or third-party    explicitly.
                                      tracking          

  Plain Language    Conditional Core  Fires from Phase  Summary appears
  Summary Block                       3 Q3              first, legal text
                                                        follows. When it
                                                        fires --- summary
                                                        is genuinely
                                                        plain. If Claude
                                                        cannot make a
                                                        section genuinely
                                                        plain --- it
                                                        notes that the
                                                        section requires
                                                        legal
                                                        translation.
  -----------------------------------------------------------------------

**Constraint Layer:** - Jurisdiction is hard stop --- locked - Last
updated date always present at top - Claude avoids jurisdiction-specific
compliance claims not confirmed in Phase 3 - No persuasion, no marketing
language, no tone adjustment - 7 sections hard maximum

**SEO Behaviour:** Phase 4 skipped entirely. Privacy/Terms pages are not
SEO targets.

------------------------------------------------------------------------

### I.F --- Family 6: Utility Pages

Contact Page, 404 Page, Thank You Page. Shared job: serve an immediate
visitor need cleanly. Function over form. Minimal friction. Brand voice
maintained but content is minimal and task-focused.

------------------------------------------------------------------------

### I.F.1 --- Contact Page

**Humanise Pass:** Full Pass

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → What people should contact for and what to filter out
→ drives form field definition and department routing - Q2 → Realistic
response time and whether to state it → drives response time expectation
block - Q3 → Contact methods available: form, phone, email, WhatsApp,
location → determines what appears - Q4 → Whether to show physical
location or service area → drives location block

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Form Field        Core              Always fires      What fields
  Definition Block                                      appear and which
                                                        are required vs
                                                        optional. Claude
                                                        defines the field
                                                        set in content.
                                                        Actual form
                                                        functionality is
                                                        development scope
                                                        --- Claude does
                                                        not build forms.

  Response Time     Supporting        Fires when Phase  Dropped if user
  Expectation Block                   3 Q2 confirms     cannot confirm
                                      user will commit  --- never
                                      to a response     invented. A
                                      time              stated response
                                                        time that is not
                                                        honoured is a
                                                        credibility risk.

  Department        Conditional Core  Fires when Phase  Maximum 3 routing
  Routing Block                       3 Q1 reveals      options. Same
                                      multiple contact  logic as Homepage
                                      purposes          Navigation
                                                        Pathway Block.

  Alternative       Supporting        Fires from Phase  Lists only
  Contact Methods                     3 Q3              confirmed
  Block                                                 channels. Never
                                                        suggests channels
                                                        user hasn't
                                                        confirmed.

  Location Block    Conditional Core  Fires from Phase  Address and
                                      3 Q4              service area both
                                                        present when it
                                                        fires for local
                                                        businesses. One
                                                        without the other
                                                        is incomplete for
                                                        local business
                                                        context.
  -----------------------------------------------------------------------

**Constraint Layer:** - Form field definition is Core - Response time
never invented - Department routing: maximum 3 options - Location block:
address + service area both present when firing - 3--4 sections hard
maximum

**SEO Behaviour:** Phase 4 fires for local businesses only. Skipped for
all other sectors.

------------------------------------------------------------------------

### I.F.2 --- 404 Page

**Core Rule:** A 404 with no way out is a dead end. Navigation recovery
is mandatory. Personality earns goodwill here --- tone should be human,
slightly self-deprecating, never corporate. Error explanation: 1--2
sentences maximum. No apology spiral.

**Humanise Pass:** Full Pass --- one of the few places where unexpected
personality earns goodwill.

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → Pages most wanted visited if someone lands here by
mistake → drives navigation recovery block - Q2 → How the brand
communicates: formal, casual, or specific personality → drives brand
tone expression - Q3 → Whether users should be able to search the site
directly from this page → drives search block

**Enrichment Component Library:**

  -------------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -------------------
  Error Explanation Core              Always fires      What happened, in
  Block                                                 plain
                                                        brand-appropriate
                                                        language. 1--2
                                                        sentences maximum.
                                                        Not a technical
                                                        error message. Not
                                                        an apology spiral.

  Navigation        Core              Always fires      Homepage link plus
  Recovery Block                                        2--3 destination
                                                        pages from Phase 3
                                                        Q1. Hard cap: 3
                                                        destination links
                                                        maximum. More than
                                                        3 creates a
                                                        navigation menu ---
                                                        defeats the purpose
                                                        of a 404 page.

  Search Block      Supporting        Fires from Phase  Structural
                                      3 Q3 when user    description only
                                      confirms site     --- actual search
                                      search exists     functionality is
                                                        development scope.
  -------------------------------------------------------------------------

**Constraint Layer:** - Navigation recovery block is Core --- a 404 with
no way out is a dead end - Maximum 3 destination links --- hard rule -
Error explanation: 1--2 sentences only - No CTAs beyond navigation
recovery - 3--4 sections hard maximum

**SEO Behaviour:** Phase 4 skipped entirely. 404 pages are not SEO
targets.

------------------------------------------------------------------------

### I.F.3 --- Thank You Page

**Core Rule:** Confirmation specificity is the trust signal. 'Your form
has been submitted' is weak. 'Your demo request is confirmed --- you'll
hear from us within one business day' is correct.

**Humanise Pass:** Full Pass

**Cross-Sell Rule --- sector-dependent:** - Default (all sectors): Soft
Cross-Sell Block is Supporting --- fires only when Phase 3 Q3 confirms a
logical adjacent offer. Never pushed. - E-commerce sector: Soft
Cross-Sell Block escalates to Conditional Core --- fires when Phase 3 Q3
or Q4 confirms a related product recommendation. Maximum 3 items.

**Phase 3 Questions (already answered in brief --- read, do not
re-ask):** - Q1 → What action just happened: form submission, purchase,
signup, or download → determines confirmation message and next steps -
Q2 → What the user should do immediately after: onboarding flow,
confirmation email, next step → drives next steps sequence - Q3 →
Whether there is a logical next offer or the page should just confirm
and close → fires or drops soft cross-sell - Q4 (E-commerce only,
Conditional Core) → Related product or next purchase to suggest, maximum
3 recommendations

**Enrichment Component Library:**

  -----------------------------------------------------------------------
  Component         Tier              Activation Signal Rules
  ----------------- ----------------- ----------------- -----------------
  Confirmation      Core              Always fires      What just
  Message Block                                         happened, stated
                                                        clearly and
                                                        specifically.
                                                        Specificity is
                                                        the trust signal.

  Next Steps        Core              Always fires      What the user
  Sequence Block                                        should do now, in
                                                        order. Maximum 3
                                                        steps. More than
                                                        3 overwhelms a
                                                        visitor in a
                                                        moment of
                                                        closure.

  Onboarding Prompt Conditional Core  Fires for SaaS or Specific action
  Block                               course signups    only --- not
                                                        'explore the
                                                        dashboard' but
                                                        'start by doing
                                                        X'. Vague prompts
                                                        are a failure
                                                        mode.

  Soft Cross-Sell   Supporting        Fires from Phase  One offer
  Block             (default) /       3 Q3 confirmation maximum. Soft
                    Conditional Core                    framing only.
                    (E-commerce)                        'Since you just
                                                        signed up for X,
                                                        the natural next
                                                        step is Y' ---
                                                        not a hard sales
                                                        push.
  -----------------------------------------------------------------------

**Constraint Layer:** - Confirmation message is Core - Next steps
maximum 3 - Onboarding prompt: specific action only --- vague direction
banned - Cross-sell: one offer, maximum 3 items, soft framing - 3--4
sections hard maximum

**SEO Behaviour:** Phase 4 skipped entirely. Thank You pages are
typically blocked from indexing.

------------------------------------------------------------------------
