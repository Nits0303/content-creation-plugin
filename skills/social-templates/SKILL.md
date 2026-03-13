# skills/social-templates/SKILL.md

## Skill Purpose

This skill is a template registry for the social generation skill. It receives a CONTENT_BRIEF, reads `CONTENT_BRIEF.sub_type`, loads the matching template definition, and returns a deterministic structural skeleton.

Templates define structural skeletons only. They specify unit structures, component tiers, activation signals, and conflict rules. They do not generate copy, define tone, perform platform algorithm logic, include humanise instructions, or contain runtime behaviour.

---

## Responsibilities

- Receive CONTENT_BRIEF from `social/SKILL.md`
- Read `CONTENT_BRIEF.sub_type`
- Match sub_type to the correct template in this registry
- Return the structural skeleton and component library for that subtype

---

## Non-Responsibilities

This skill must NOT:

- Ask questions
- Modify CONTENT_BRIEF
- Generate copy or prose
- Apply tone rules
- Perform SEO logic
- Run sector overlays
- Run humanise passes
- Interpret platform algorithms
- Apply hashtag logic (owned by generation skill)
- Apply duration-to-structure translation (owned by generation skill)

---

## Template Selection Logic

```
RECEIVE CONTENT_BRIEF
READ CONTENT_BRIEF.sub_type

MATCH sub_type TO template:

  FAMILY 1 — SINGLE-UNIT POSTS
  "text-post"              → Template J.A.1
  "image-caption"          → Template J.A.2
  "announcement-post"      → Template J.A.3
  "promotional-post"       → Template J.A.4
  "engagement-post"        → Template J.A.5
  "event-post"             → Template J.A.6
  "testimonial-post"       → Template J.A.7
  "hashtag-campaign-post"  → Template J.A.8

  FAMILY 2 — MULTI-UNIT FORMATS
  "carousel"               → Template J.B.1
  "story"                  → Template J.B.2
  "poll"                   → Template J.B.3
  "thread"                 → Template J.B.4

  FAMILY 3 — LONG-FORM & SCRIPTED CONTENT
  "short-video-script"     → Template J.C.1
  "long-video-script"      → Template J.C.2
  "linkedin-article"       → Template J.C.3

  NO MATCH → return error to social/SKILL.md: "sub_type not recognised"

RETURN structural skeleton + component library
```

---

## Social Template Library

---

### FAMILY 1 — SINGLE-UNIT POSTS

**Family shared structure:** Single content block output. Hook-first. Feed Compression Rule applies — core value visible before platform truncation boundary. One dominant idea. One dominant action. Platform-adaptive copy length. Hashtag logic applied by generation skill from `CONTENT_BRIEF.platform`.

---

#### Template J.A.1 — Text Post

**Unit Structure:** Single block. No visual dependency.

**Structural Order:**
1. Hook
2. Body
3. CTA / Closing

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook | Core | Structural | Always fires. First element. Feed Compression Rule applies | None |
| Body | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` | None |
| CTA / Closing | Conditional Core | Structural | Fires when `CONTENT_BRIEF.cta_allowed` is `true` | When `cta_allowed` is `false` — closing line replaces CTA |
| Hashtag Block | Supporting | Structural | Fires when `CONTENT_BRIEF.platform` is populated. Applied at end of unit | Conflicts with body-embedded hashtags — end placement only |

---

#### Template J.A.2 — Image Caption

**Unit Structure:** Single block. Caption is context layer over a visual asset.

**Structural Order:**
1. Hook
2. Caption Body
3. CTA / Closing

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook | Core | Structural | Always fires. Feed Compression Rule applies | None |
| Caption Body | Core | Structural | Always fires. Shorter than Text Post body. Reads from `CONTENT_BRIEF.content_points` | None |
| Visual Asset Reference | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.brand_inclusions` for asset description | Generation does not proceed without visual asset entry in `brand_inclusions` |
| CTA / Closing | Conditional Core | Structural | Fires when `CONTENT_BRIEF.cta_allowed` is `true` | None |
| Hashtag Block | Supporting | Structural | Fires when `CONTENT_BRIEF.platform` is populated | Conflicts with body-embedded hashtags — end placement only |

---

#### Template J.A.3 — Announcement Post

**Unit Structure:** Single block. Time and action anchored.

**Structural Order:**
1. Hook
2. Announcement Body — What / When / Action
3. CTA

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook | Core | Structural | Always fires. Feed Compression Rule applies | None |
| Announcement Body | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points`. Must include: what, timing, action intent | None |
| CTA | Core | Structural | Always fires. Announcement posts require a CTA regardless of `cta_type` | None |
| Urgency / Timing Signal | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a timing or deadline entry | None |
| Hashtag Block | Supporting | Structural | Fires when `CONTENT_BRIEF.platform` is populated | End placement only |

---

#### Template J.A.4 — Promotional Post

**Unit Structure:** Single block. Offer-driven. CTA is Core — no exceptions.

**Structural Order:**
1. Hook
2. Offer Block — offer, mechanic, outcome
3. CTA

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook | Core | Structural | Always fires. Feed Compression Rule applies | None |
| Offer Block | Core | Structural | Always fires. Three elements required: offer, mechanic, outcome. Reads from `CONTENT_BRIEF.content_points` | None |
| CTA | Core | Structural | Always fires. CTA is mandatory regardless of `CONTENT_BRIEF.cta_type` | None |
| Social Proof Signal | Conditional Core | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` contains a proof or result entry | Conflicts with fabricated claims — fires only when entry is present |
| Third-Party Reference | Supporting | Narrative | Fires when `CONTENT_BRIEF.third_party_mentions` is populated | None |
| Hashtag Block | Supporting | Structural | Fires when `CONTENT_BRIEF.platform` is populated | End placement only |

---

#### Template J.A.5 — Engagement Post (Question / CTA)

**Unit Structure:** Single block. Question is the dominant action.

**Structural Order:**
1. Hook / Opening Frame
2. Question or Prompt
3. Closing (optional follow-on)

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook / Opening Frame | Core | Structural | Always fires. Feed Compression Rule applies | None |
| Engagement Question | Core | Structural | Always fires. Question IS the CTA. Reads from `CONTENT_BRIEF.content_points` | Conflicts with a separate CTA block — question replaces CTA |
| Research Frame | Conditional Core | Narrative | Fires when `CONTENT_BRIEF.content_goal` is `engage` and `CONTENT_BRIEF.content_points` contains a research intent entry | Conflicts with Community Frame — only one intent frame fires |
| Community Frame | Conditional Core | Narrative | Fires when `CONTENT_BRIEF.content_goal` is `engage` and `CONTENT_BRIEF.content_points` contains a community intent entry | Conflicts with Research Frame — only one intent frame fires |
| Follow-On Prompt | Supporting | Structural | Fires when `CONTENT_BRIEF.content_points` contains a secondary prompt entry | None |
| Hashtag Block | Supporting | Structural | Fires when `CONTENT_BRIEF.platform` is populated | End placement only |

---

#### Template J.A.6 — Event Post

**Unit Structure:** Single block. Logistics-anchored. Pre-event and post-event variants.

**Structural Order:**
1. Hook
2. Event Details Block
3. CTA

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook | Core | Structural | Always fires. Feed Compression Rule applies | None |
| Event Details Block | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points`. Logistics completeness checked by generation skill | None |
| CTA | Core | Structural | Always fires | None |
| Post-Event Variant Frame | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a post-event entry | Conflicts with pre-event structure — only one variant fires per brief |
| Visual / Asset Reference | Conditional Core | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` contains an event asset entry | None |
| Hashtag Block | Supporting | Structural | Fires when `CONTENT_BRIEF.platform` is populated | End placement only |

---

#### Template J.A.7 — Testimonial Post

**Unit Structure:** Single block. Source material anchored. No invented content.

**Structural Order:**
1. Hook / Setup Frame
2. Testimonial Quote Block
3. Attribution
4. CTA / Closing

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook / Setup Frame | Core | Structural | Always fires. Feed Compression Rule applies | None |
| Testimonial Quote Block | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.brand_inclusions` for source material. Generation does not proceed without a testimonial entry | None |
| Attribution | Core | Structural | Always fires. Attribution floor: name minimum. Reads from `CONTENT_BRIEF.brand_inclusions` | None |
| Result or Outcome Highlight | Conditional Core | Narrative | Fires when `CONTENT_BRIEF.content_points` contains a result or metric entry | None |
| CTA / Closing | Conditional Core | Structural | Fires when `CONTENT_BRIEF.cta_allowed` is `true` | None |
| Hashtag Block | Supporting | Structural | Fires when `CONTENT_BRIEF.platform` is populated | End placement only |

---

#### Template J.A.8 — Hashtag Campaign Post

**Unit Structure:** Single block. Campaign hashtag anchored.

**Structural Order:**
1. Hook
2. Campaign Body
3. Campaign Hashtag(s)
4. CTA / Closing

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook | Core | Structural | Always fires. Feed Compression Rule applies | None |
| Campaign Body | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` | None |
| Campaign Hashtag Anchor | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.special_instructions` for campaign hashtag entry | Generation does not proceed without campaign hashtag entry |
| Originating Variant Frame | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains an originating campaign entry | Conflicts with Participation Variant Frame — only one fires |
| Participation Variant Frame | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a participation campaign entry | Conflicts with Originating Variant Frame — only one fires |
| CTA / Closing | Conditional Core | Structural | Fires when `CONTENT_BRIEF.cta_allowed` is `true` | None |
| Supporting Hashtag Block | Supporting | Structural | Fires when `CONTENT_BRIEF.platform` is populated. Secondary hashtags appended after campaign anchor | Conflicts with campaign anchor — campaign anchor always placed first |

---

### FAMILY 2 — MULTI-UNIT FORMATS

**Family shared structure:** Sequential discrete unit output. Hook Unit fires first. Progressive value across units. Transition Logic between units (Carousel, Story, Thread). Closing Unit with CTA. `CONTENT_BRIEF.unit_count` determines sequence length. `CONTENT_BRIEF.arc_type` determines unit progression pattern. Family 2 arc_type values: `list` | `build` | `reveal` | `problem-solution`.

**Poll exception:** Poll is a compressed multi-unit subtype. Transition Logic and Closing Unit do not apply to Poll.

---

#### Template J.B.1 — Carousel

**Unit Structure:** Sequential slides. Hook Unit → Content Units → Closing Unit.

**Structural Order:**
1. Hook Unit (Slide 1)
2. Content Units (Slides 2–N, count from `CONTENT_BRIEF.unit_count`)
3. Closing Unit with CTA (Final Slide)

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook Unit | Core | Structural | Always fires. Slide 1. Reads from `CONTENT_BRIEF.content_points` | None |
| Content Units | Core | Structural | Always fires. Count reads from `CONTENT_BRIEF.unit_count`. Each unit carries one idea. Progressive value across sequence | None |
| Arc Pattern | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.arc_type`. Determines unit-to-unit progression logic | None |
| Transition Logic | Core | Structural | Always fires. Each non-closing unit links to next | Absent from Poll template — carousel-specific |
| Closing Unit | Core | Structural | Always fires. Final slide. Reads from `CONTENT_BRIEF.cta_type` | None |
| Visual Slot Marker | Conditional Core | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` contains visual asset entries. One slot marked per slide | None |
| Cover Text Overlay | Supporting | Structural | Fires when `CONTENT_BRIEF.special_instructions` contains a cover text entry | None |

---

#### Template J.B.2 — Story

**Unit Structure:** Sequential frames. Hook Frame → Content Frames → Closing Frame.

**Structural Order:**
1. Hook Frame (Frame 1)
2. Content Frames (Frames 2–N, count from `CONTENT_BRIEF.unit_count`)
3. Closing Frame with CTA or Interactive Element

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook Frame | Core | Structural | Always fires. Frame 1. Reads from `CONTENT_BRIEF.content_points` | None |
| Content Frames | Core | Structural | Always fires. Count reads from `CONTENT_BRIEF.unit_count`. Each frame carries one moment or idea | None |
| Arc Pattern | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.arc_type` | None |
| Transition Logic | Core | Structural | Always fires between non-closing frames | None |
| Closing Frame | Core | Structural | Always fires. Final frame. Reads from `CONTENT_BRIEF.cta_type` | None |
| Visual Slot Marker | Conditional Core | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` contains visual asset entries | None |
| Interactive Element Slot | Conditional Core | Structural | Fires when `CONTENT_BRIEF.special_instructions` contains an interactive element entry (poll sticker, question sticker, link sticker) | None |

---

#### Template J.B.3 — Poll

**Unit Structure:** Compressed multi-unit. Question Unit → Option Units. No Transition Logic. No Closing Unit.

**Structural Order:**
1. Poll Question
2. Poll Options (count from `CONTENT_BRIEF.unit_count` or default 2–4)
3. Setup Context (optional, pre-question)

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Poll Question | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` for question entry. Generation does not proceed without a question entry | None |
| Poll Options | Core | Structural | Always fires. Count reads from `CONTENT_BRIEF.unit_count`. Minimum 2, maximum 4. Parallelism enforced — all options must match grammatical structure | None |
| Setup Context | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a context framing entry. Appears before question | None |
| Follow-On Prompt | Supporting | Structural | Fires when `CONTENT_BRIEF.special_instructions` contains a follow-on prompt entry | None |

---

#### Template J.B.4 — Thread (X / Twitter)

**Unit Structure:** Sequential posts. Hook Post → Content Posts → Closing Post. Each post standalone-readable.

**Structural Order:**
1. Hook Post (Post 1)
2. Content Posts (Posts 2–N, count from `CONTENT_BRIEF.unit_count`)
3. Closing Post with CTA

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hook Post | Core | Structural | Always fires. Post 1. Reads from `CONTENT_BRIEF.content_points` | None |
| Content Posts | Core | Structural | Always fires. Count reads from `CONTENT_BRIEF.unit_count`. Each post is standalone-readable — value delivered within each post, not only across the sequence | None |
| Arc Pattern | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.arc_type` | None |
| Transition Logic | Core | Structural | Always fires between non-closing posts. Transition is numbering or connector phrase — not a cliffhanger that breaks standalone rule | Conflicts with cliffhanger-only transitions — standalone standard overrides |
| Closing Post | Core | Structural | Always fires. Final post. Reads from `CONTENT_BRIEF.cta_type` | None |
| Third-Party Reference | Supporting | Narrative | Fires when `CONTENT_BRIEF.third_party_mentions` is populated | None |

---

### FAMILY 3 — LONG-FORM & SCRIPTED CONTENT

**Family shared structure:** Structured long-form document output. Opening Hook, Core Sections with progressive development, Supporting Examples, Closing Resolution, optional CTA. `CONTENT_BRIEF.duration_target` feeds duration-to-structure translation before generation. `CONTENT_BRIEF.arc_type` determines section progression. Family 3 arc_type values: `tutorial` | `essay` | `narrative` | `argument`. Video scripts additionally include Scene Direction Layer — text instructions only, no media generated.

---

#### Template J.C.1 — Short Video Script (Reels / TikTok)

**Unit Structure:** Single scripted document with Scene Direction Layer. Hook → Core → Resolution.

**Structural Order:**
1. Opening Hook
2. Core Section(s)
3. Closing Resolution
4. CTA (optional)

**Scene Direction Layer** runs in parallel with spoken content throughout all sections when active.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Opening Hook | Core | Structural | Always fires. Single takeaway established at hook. Reads from `CONTENT_BRIEF.content_points` | None |
| Core Section | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.duration_target` for section count via duration-to-structure translation | None |
| Closing Resolution | Core | Structural | Always fires | None |
| Scene Direction Layer | Core | Structural | Always fires for this subtype. Parallel layer alongside spoken content. Contains: visual concept notes, on-screen text suggestions, transition cues | Conflicts with blog and webpage output formats — script format only |
| On-Camera Direction | Conditional Core | Structural | Fires when `CONTENT_BRIEF.delivery_mode` is `on-camera`. Speaker blocking notes and eye-line direction included in Scene Direction Layer | Conflicts with Voiceover Direction and Text-on-Screen Direction — only one delivery mode fires |
| Voiceover Direction | Conditional Core | Structural | Fires when `CONTENT_BRIEF.delivery_mode` is `voiceover`. B-roll description notes replace speaker direction in Scene Direction Layer | Conflicts with On-Camera Direction and Text-on-Screen Direction |
| Text-on-Screen Direction | Conditional Core | Structural | Fires when `CONTENT_BRIEF.delivery_mode` is `text-on-screen`. Title card sequences in Scene Direction Layer. Output structurally closer to Carousel | Conflicts with On-Camera Direction and Voiceover Direction |
| CTA | Conditional Core | Structural | Fires when `CONTENT_BRIEF.cta_allowed` is `true` | None |
| Brand Inclusion Marker | Supporting | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` is populated | None |

---

#### Template J.C.2 — Long Video Script (YouTube)

**Unit Structure:** Single scripted document with Scene Direction Layer. Multi-section structure. Hook → Core Sections → Resolution.

**Structural Order:**
1. Opening Hook
2. Core Sections (multiple, count from `CONTENT_BRIEF.duration_target`)
3. Supporting Examples
4. Closing Resolution
5. CTA (optional)

**Scene Direction Layer** runs in parallel with spoken content throughout all sections.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Opening Hook | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` for hook mechanism | None |
| Core Sections | Core | Structural | Always fires. Multiple sections. Count reads from `CONTENT_BRIEF.duration_target` via duration-to-structure translation. Arc reads from `CONTENT_BRIEF.arc_type` | None |
| Supporting Examples | Core | Structural | Always fires. Minimum one per core section. Reads from `CONTENT_BRIEF.content_points` | None |
| Closing Resolution | Core | Structural | Always fires | None |
| Scene Direction Layer | Core | Structural | Always fires for this subtype. Contains: visual concept notes, action descriptions, on-screen text suggestions, transition cues | Conflicts with blog and webpage output formats — script format only |
| CTA | Conditional Core | Structural | Fires when `CONTENT_BRIEF.cta_allowed` is `true` | None |
| Third-Party Reference Block | Conditional Core | Narrative | Fires when `CONTENT_BRIEF.third_party_mentions` is populated | None |
| Brand Inclusion Marker | Supporting | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` is populated | None |

---

#### Template J.C.3 — LinkedIn Article

**Unit Structure:** Single structured article document. No Scene Direction Layer.

**Structural Order:**
1. Opening Hook
2. Core Sections
3. Supporting Examples
4. Closing Resolution
5. CTA (optional)

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Opening Hook | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` | None |
| Core Sections | Core | Structural | Always fires. Architecture determined by `CONTENT_BRIEF.article_type`. Arc reads from `CONTENT_BRIEF.arc_type`. Count informed by `CONTENT_BRIEF.duration_target` | None |
| Supporting Examples | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` | None |
| Closing Resolution | Core | Structural | Always fires | None |
| Thought Leadership Frame | Conditional Core | Structural | Fires when `CONTENT_BRIEF.article_type` is `thought-leadership` or `argument`. Thesis-first structure | Conflicts with Tutorial Frame and Personal Narrative Frame |
| Tutorial Frame | Conditional Core | Structural | Fires when `CONTENT_BRIEF.article_type` is `how-to`. Step-sequence core section structure | Conflicts with Thought Leadership Frame and Personal Narrative Frame |
| Industry Analysis Frame | Conditional Core | Structural | Fires when `CONTENT_BRIEF.article_type` is `industry-analysis`. Data or trend anchored core sections | Conflicts with Tutorial Frame and Personal Narrative Frame |
| Personal Narrative Frame | Conditional Core | Structural | Fires when `CONTENT_BRIEF.article_type` is `personal-narrative`. Story arc core section structure | Conflicts with Thought Leadership Frame and Tutorial Frame |
| Case Study Frame | Conditional Core | Structural | Fires when `CONTENT_BRIEF.article_type` is `case-study`. Challenge-solution-results structure in core sections | Conflicts with Tutorial Frame and Personal Narrative Frame |
| CTA | Conditional Core | Structural | Fires when `CONTENT_BRIEF.cta_allowed` is `true` | None |
| Third-Party Reference Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.third_party_mentions` is populated | None |
| Brand Inclusion Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.brand_inclusions` is populated | None |

---

## Family Structure Reference

| Family | Subtypes | Key Structural Constraints |
|---|---|---|
| Family 1 — Single-Unit Posts | Text Post, Image Caption, Announcement Post, Promotional Post, Engagement Post, Event Post, Testimonial Post, Hashtag Campaign Post | Single content block. Hook-first. Feed Compression Rule. One dominant idea. One dominant action |
| Family 2 — Multi-Unit Formats | Carousel, Story, Poll, Thread | Sequential discrete units. Hook Unit first. Progressive value. Transition Logic (not Poll). Closing Unit with CTA (not Poll). `unit_count` and `arc_type` required |
| Family 3 — Long-Form & Scripted | Short Video Script, Long Video Script, LinkedIn Article | Structured long-form output. Opening Hook, Core Sections, Supporting Examples, Closing Resolution. `duration_target` and `arc_type` required. Video scripts include Scene Direction Layer |
