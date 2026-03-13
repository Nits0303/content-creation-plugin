# Blog Templates Skill

## Purpose

This skill provides structural templates for all 12 blog subtypes. It is consumed by the blog generation skill before drafting begins.

This skill does not generate content. It does not write sentences or paragraphs. It does not perform SEO checks. It does not modify `CONTENT_BRIEF`. It returns a deterministic structural skeleton for the requested subtype.

---

## Pipeline Position

```
/create → intake → router → blog/SKILL.md → [loads blog-templates/SKILL.md] → humanise
```

This skill is not a generation skill. It is a structural loader called by `blog/SKILL.md`. It fires after `CONTENT_BRIEF` is assembled and before any writing begins.

---

## Inputs

The blog-templates skill reads from `CONTENT_BRIEF`:

| Field | Use |
|---|---|
| `format` | Must be `blog` — if not, this skill does not load |
| `sub_type` | Determines which template is returned |
| `length_tier` | Passed to the generation skill for supporting component limit calculation |

The skill never modifies `CONTENT_BRIEF`.

---

## Supported Blog Subtypes

All 12 subtypes are supported:

- `howto` — How-to / Tutorial
- `listicle` — Listicle
- `opinion` — Opinion / Thought Leadership
- `news-trends` — News & Updates
- `product-review` — Product Review
- `comparison` — Comparison Post
- `casestudy` — Case Study
- `interview` — Interview
- `guest-post` — Guest Post
- `seo-article` — SEO-Focused Article
- `beginners-guide` — Beginner's Guide
- `ultimate-guide` — Ultimate Guide / Pillar Post

---

## Template Responsibilities

The blog-templates skill defines:

- Article skeleton structure and H2 section order
- Required structural components (Core)
- Conditional components and their activation signals
- Supporting and enrichment components with conflict rules
- Subtype-specific section ceilings

The blog-templates skill does not define:

- Writing rules or tone guidance
- SEO logic or keyword handling
- Humanise pass behaviour
- Intake question logic
- Sector overlay behaviour

All of the above are owned by their respective skills.

---

## Global Structure Rules

### Maximum Section Rule

Default maximum: **8 H2 sections per blog post.**

Exception: Ultimate Guide may reach up to **10 H2 sections**.

When activated components would exceed the ceiling, the generation skill must consolidate rather than add sections.

### Component Priority Tiers

Every component belongs to one of four tiers:

| Tier | Definition |
|---|---|
| **Core** | Always fires. No activation condition required. |
| **Conditional Core** | Fires when a specific activation signal is met. When met, it is mandatory. |
| **Supporting** | Fires when the activation signal is met and the supporting component limit allows. |
| **Enrichment** | Lowest priority. Fires only when explicitly available from the brief and length permits. |

### Supporting Component Limit by Length Tier

The template exposes the supporting component list. Activation is handled by the generation skill.

| Length Tier | Max Supporting Components |
|---|---|
| Short (600–900 words) | 1 |
| Medium (900–1,400 words) | 2 |
| Long (1,400+ words) | 3 |

### Component Metadata Fields

Each component entry in the templates below contains:

- **Tier** — Core / Conditional Core / Supporting / Enrichment
- **Type** — Structural or Narrative
- **Activation Signal** — condition that fires the component
- **Conflict Rule** — components that cannot coexist or are reduced when this fires

---

## Subtype Template Definitions

---

### F.1 — How-to / Tutorial

```
TEMPLATE = {
  sub_type: "howto",
  max_sections: 8,

  core_sections: [
    "Introduction",
    "Prerequisites Block",
    "Numbered Step Sequence",
    "Conclusion"
  ],

  components: [
    {
      name: "Prerequisites Block",
      tier: "Core",
      type: "Structural",
      activation: "Always fires for Beginner. Optional for Intermediate and Advanced.",
      conflict: "None"
    },
    {
      name: "Tools / Materials List",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires for physical process how-tos. Optional for software/conceptual.",
      conflict: "None"
    },
    {
      name: "Time / Effort Estimate",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires at all lengths. High value for reader self-selection.",
      conflict: "None"
    },
    {
      name: "Checkpoint Callouts",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory for Beginner. Supporting for Intermediate. Absent for Advanced.",
      conflict: "None"
    },
    {
      name: "Embedded Troubleshooting",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires above 1,200 words. Placed at the step where failure typically occurs — not at the bottom.",
      conflict: "None"
    },
    {
      name: "Variations Section",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires above 1,500 words only if brief contains that knowledge. Never fabricated.",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic affects a specific identifiable community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it.",
      conflict: "None"
    },
    {
      name: "Related Tutorials Link Block",
      tier: "Enrichment",
      type: "Structural",
      activation: "Eligible at all lengths. Internal link placeholders only.",
      conflict: "None"
    }
  ]
}
```

---

### F.2 — Listicle

```
TEMPLATE = {
  sub_type: "listicle",
  max_sections: 8,

  core_sections: [
    "Introduction",
    "Numbered List Items",
    "Conclusion"
  ],

  components: [
    {
      name: "Table of Contents",
      tier: "Core",
      type: "Structural",
      activation: "Eligible above 1,200 words.",
      conflict: "None"
    },
    {
      name: "Categorised Sub-grouping",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when list has 8+ items AND items naturally segment into 2–4 distinct groups. Never at the beginning.",
      conflict: "None"
    },
    {
      name: "Budget Tier Layer",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when items have pricing context and pricing is verifiable.",
      conflict: "None"
    },
    {
      name: "Expert Quote / Roundup",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires for third-party listicles above 1,500 words. Never fabricated. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "FAQ Section",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires for third-party and own-product lists.",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic affects a specific community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it.",
      conflict: "None"
    }
  ]
}
```

---

### F.3 — Opinion / Thought Leadership

```
TEMPLATE = {
  sub_type: "opinion",
  max_sections: 8,

  core_sections: [
    "Stance + Opening",
    "Core Argument Body",
    "Counter-Argument Block",
    "Conclusion"
  ],

  components: [
    {
      name: "Stance + Opening",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Must name the stance in the first paragraph.",
      conflict: "None"
    },
    {
      name: "Counter-Argument Block",
      tier: "Core",
      type: "Narrative",
      activation: "Always fires. Must engage the strongest version of the opposing view.",
      conflict: "None"
    },
    {
      name: "Industry Context Block",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Mandatory for Industry Analysis type. Supporting for other types. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Story Block",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Mandatory for Experience-backed type. Drops for Counter-narrative and Industry Analysis.",
      conflict: "None"
    },
    {
      name: "Evidence / Data Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when user provides evidence or credible supporting data is available. Named sources only. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Credibility Anchor",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when user provides personal connection to topic. Never fabricated. Dropped if skipped.",
      conflict: "None"
    },
    {
      name: "Context Bridge",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when audience familiarity is uninformed or mixed. Dropped for informed audiences.",
      conflict: "None"
    },
    {
      name: "Author Bio",
      tier: "Supporting",
      type: "Narrative",
      activation: "Strongly recommended for all Opinion pieces. Credibility matters more here than any other subtype. User must provide.",
      conflict: "None"
    }
  ]
}
```

---

### F.4 — News & Updates

```
TEMPLATE = {
  sub_type: "news-trends",
  max_sections: 6,

  core_sections: [
    "Aggressive Headline",
    "Three-Part Opening Sequence",
    "Context Block",
    "Implication Layer"
  ],

  components: [
    {
      name: "Aggressive Headline (3 options)",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Three variants: urgency, stakes, contrarian. User picks one.",
      conflict: "None"
    },
    {
      name: "Three-Part Opening Sequence",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Part 1: curiosity builder. Part 2: reveal. Part 3: body opens.",
      conflict: "None"
    },
    {
      name: "Context Block",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. What happened, when, who, why significant. Complete account before analysis begins.",
      conflict: "None"
    },
    {
      name: "Implication Layer",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. The 'so what' for the reader. Mandatory.",
      conflict: "None"
    },
    {
      name: "Brand Angle Block",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Mandatory when content intent is opinionated commentary. Drops to Supporting for neutral reporting. Confirmed zone — comes entirely from user input.",
      conflict: "None"
    },
    {
      name: "Timeline Snapshot",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when news event involves multiple sequential developments across more than one day OR multiple parties responding to each other over time. Maximum 6 date-stamped entries. Factual only.",
      conflict: "None"
    },
    {
      name: "Authority Quote Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic involves expert analysis, statistics are mentioned, or topic is controversial or high-stakes. Named credible sources only. Drop if nothing credible found. [F.0.3 applies]",
      conflict: "If substantive, Perspectives Block becomes optional."
    },
    {
      name: "Perspectives Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when multiple credible voices have publicly responded to the event. Three variants: public sentiment, expert opinion, brand take. [F.0.3 applies]",
      conflict: "Optional if Authority Quote Block already covers external viewpoints sufficiently."
    },
    {
      name: "What Happens Next Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when story is ongoing — announced plans, scheduled developments, or anticipated responses exist. Speculation labelled 'expected' or 'anticipated' — never stated as confirmed. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Origin-to-Present Context Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when news event involves a complex or niche subject the average reader may not have background on. 150–300 words. Mandatory for niche topics with mixed audiences.",
      conflict: "If Context Block already covers sufficient background — reduce to a brief paragraph rather than a full block."
    },
    {
      name: "Community Relevance Block",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when topic affects a specific identifiable audience or industry group. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it.",
      conflict: "None"
    }
  ]
}
```

---

### F.5 — Product Review

```
TEMPLATE = {
  sub_type: "product-review",
  max_sections: 8,

  core_sections: [
    "Quick Take Summary Box",
    "Introduction",
    "Reviewer Credibility Block",
    "Product Overview",
    "Pros Block",
    "Cons Block",
    "Who It's For / Who It's Not For",
    "Verdict and Recommendation Block"
  ],

  components: [
    {
      name: "Quick Take Summary Box",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Placed at the top before the intro.",
      conflict: "None"
    },
    {
      name: "Reviewer Credibility Block",
      tier: "Core",
      type: "Narrative",
      activation: "Always fires. First or second paragraph.",
      conflict: "None"
    },
    {
      name: "Product Overview",
      tier: "Core",
      type: "Structural",
      activation: "Always fires.",
      conflict: "None"
    },
    {
      name: "Pros Block",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Minimum 3 pros.",
      conflict: "None"
    },
    {
      name: "Cons Block",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Three-tier con classification applies. Minimum 1 critical con mandatory.",
      conflict: "None"
    },
    {
      name: "Who It's For / Who It's Not For",
      tier: "Core",
      type: "Narrative",
      activation: "Always fires. Both sides mandatory.",
      conflict: "None"
    },
    {
      name: "Verdict and Recommendation Block",
      tier: "Core",
      type: "Structural",
      activation: "Always fires — placed at the close. Verdict misalignment rule applies.",
      conflict: "None"
    },
    {
      name: "In-Depth Experience / Testing Section",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Mandatory when user has personal experience with the product. Drops to Supporting when review is research-based only.",
      conflict: "If user has no personal experience → drops to Supporting."
    },
    {
      name: "Pricing Breakdown",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when product has pricing tiers, subscription models, or non-obvious pricing structure. Never fabricated. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Alternatives Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when verdict is mixed or critical, or when product clearly suits only a segment. Maximum 3 alternatives. Drop if user's goal is purely promotional.",
      conflict: "None"
    },
    {
      name: "User Testimonials / Social Proof",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when review is research-based rather than hands-on, or when length justifies. Attributed to source. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "FAQ Section",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when product has common objections or frequently searched questions.",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when product serves a specific identifiable community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it.",
      conflict: "None"
    }
  ]
}
```

---

### F.6 — Comparison Post

```
TEMPLATE = {
  sub_type: "comparison",
  max_sections: 8,

  core_sections: [
    "Headline",
    "Decision Framing Introduction",
    "Quick Comparison Summary Box",
    "Individual Option Overviews",
    "Criteria-Based Comparison Sections",
    "Use Case Segmentation Block"
  ],

  components: [
    {
      name: "Headline",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Must contain 'vs' or 'or' or 'alternatives'. Two headline variants.",
      conflict: "None"
    },
    {
      name: "Decision Framing Introduction",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. States what is being compared, who it is for, and what criteria will be used. Bias disclosure lives here if applicable.",
      conflict: "None"
    },
    {
      name: "Quick Comparison Summary Box",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Placed immediately after introduction.",
      conflict: "None"
    },
    {
      name: "Individual Option Overviews",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. One brief section per option.",
      conflict: "None"
    },
    {
      name: "Criteria-Based Comparison Sections",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Minimum 4 criteria. Maximum 8 criteria. Each section: criterion name → how Option A performs → how Option B performs → which wins for which reader type.",
      conflict: "None"
    },
    {
      name: "Use Case Segmentation Block",
      tier: "Core",
      type: "Narrative",
      activation: "Always fires — this is the conclusion. Minimum two distinct reader profiles. No 'it depends' conclusions.",
      conflict: "None"
    },
    {
      name: "Comparison Table",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory when comparing products with multiple comparable specifications. Supporting for approach comparisons where specs do not apply.",
      conflict: "None"
    },
    {
      name: "Bias Disclosure Block",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory when user's own product is one of the options being compared. Placed in the introduction. 1–2 sentences.",
      conflict: "None"
    },
    {
      name: "Pricing Comparison",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when options have different pricing structures or when price is a key decision driver. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "User Sentiment Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when options are publicly known and have verifiable user reviews. Attributed to source. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "FAQ Section",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when frequently searched questions exist for the comparison keyword.",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when comparison topic affects a specific identifiable community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it.",
      conflict: "None"
    }
  ]
}
```

---

### F.7 — Case Study

```
TEMPLATE = {
  sub_type: "casestudy",
  max_sections: 8,

  core_sections: [
    "Headline with Result",
    "Quick Summary Box",
    "Problem Statement",
    "Strategy",
    "Implementation",
    "Measurable Results",
    "Closing"
  ],

  components: [
    {
      name: "Headline with Result",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Lead with the outcome metric.",
      conflict: "None"
    },
    {
      name: "Quick Summary Box",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Placed at top before any body content.",
      conflict: "None"
    },
    {
      name: "Problem Statement",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. 1–2 sentences. Formally stated. Distinct from the Challenge Detail.",
      conflict: "None"
    },
    {
      name: "Strategy",
      tier: "Core",
      type: "Narrative",
      activation: "Always fires. Strategic reasoning, not action steps.",
      conflict: "None"
    },
    {
      name: "Implementation",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Separated from Strategy deliberately.",
      conflict: "None"
    },
    {
      name: "Measurable Results",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Specific metrics. Approximate numbers acceptable. Vague descriptors never acceptable.",
      conflict: "None"
    },
    {
      name: "Closing",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. 1–2 sentences. Orients reader toward their own next step.",
      conflict: "None"
    },
    {
      name: "Before vs After Comparison",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory when user has clear before-state metrics. Drops to Supporting when only after-metrics are available.",
      conflict: "None"
    },
    {
      name: "Client Quote Block",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Mandatory for client case studies when client has given permission and provided a quote. Supporting for self case studies. Never fabricated. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Background Context",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when case involves a niche topic or when the reader may lack industry context. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Objectives",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when user can provide the measurable goals set before the work began. Different from results — objectives are the target, results are what happened.",
      conflict: "None"
    },
    {
      name: "User Personas",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when the work involved understanding a specific audience segment. User must provide.",
      conflict: "None"
    },
    {
      name: "Insights and Takeaways",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires above 1,000 words. Key insights discovered during the process plus lessons the reader can apply.",
      conflict: "None"
    },
    {
      name: "Next Steps",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when user can provide continued client engagement details or a natural recommendation for the reader facing a similar challenge.",
      conflict: "None"
    },
    {
      name: "Process Walkthrough",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when the solution benefits from being shown in sequential steps beyond the Implementation section.",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when the case study topic affects a specific identifiable industry community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it.",
      conflict: "None"
    }
  ]
}
```

---

### F.8 — Interview

```
TEMPLATE = {
  sub_type: "interview",
  max_sections: 8,

  core_sections: [
    "Subject Introduction",
    "Framing Intro",
    "Interview Body",
    "Quote Density Rule",
    "Closing Reflection"
  ],

  components: [
    {
      name: "Subject Introduction",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. One paragraph.",
      conflict: "None"
    },
    {
      name: "Framing Intro",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. 2–3 sentences maximum.",
      conflict: "None"
    },
    {
      name: "Interview Body",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Format determined by routing (Q&A, Narrative, Hybrid).",
      conflict: "None"
    },
    {
      name: "Quote Density Rule",
      tier: "Core",
      type: "Narrative",
      activation: "Always enforced. Interviewee quotes must occupy at least 60% of the article in narrative and hybrid formats.",
      conflict: "None"
    },
    {
      name: "Closing Reflection",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Final quote selected for resonance. Never a summary.",
      conflict: "None"
    },
    {
      name: "Pull Quote Highlight",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Fires when one answer contains a contrarian statement, strong opinion, memorable phrasing, or shareable insight strong enough to stand alone as a section anchor.",
      conflict: "None"
    },
    {
      name: "Background / Origin Story",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when interviewee's journey is part of what makes their perspective credible. Drops or is absorbed if Subject Introduction already covers origin.",
      conflict: "None"
    },
    {
      name: "Lessons / Takeaways Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires above 1,200 words when interviewee has provided transferable insights. Must come from interviewee's answers — never invented.",
      conflict: "None"
    },
    {
      name: "Editor's Note / Context Insert",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when interview references an event or term the general reader may not have context for. One sentence, bracketed.",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it.",
      conflict: "None"
    }
  ]
}
```

---

### F.9 — Guest Post

```
TEMPLATE = {
  sub_type: "guest-post",
  max_sections: 8,

  core_sections: [
    "Author Introduction",
    "Unique Angle Block",
    "Context Bridge",
    "Body Argument",
    "Author Bio CTA"
  ],

  components: [
    {
      name: "Author Introduction",
      tier: "Core",
      type: "Structural",
      activation: "Always fires unless author is anonymous.",
      conflict: "Drops entirely when anonymous route is selected."
    },
    {
      name: "Unique Angle Block",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Stated in the first two paragraphs.",
      conflict: "None"
    },
    {
      name: "Body Argument",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Built for host platform's audience throughout.",
      conflict: "None"
    },
    {
      name: "Author Bio CTA",
      tier: "Core",
      type: "Structural",
      activation: "Always fires unless anonymous. One link, one action. Never in the conclusion.",
      conflict: "Drops when anonymous route is selected."
    },
    {
      name: "Context Bridge",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory when cold audience is detected. Curiosity-led opening fires with it. Drops for warm audience.",
      conflict: "None"
    },
    {
      name: "Body Contextual Mention",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Fires only when brand directly supports the point being made. One mention maximum. Never forced.",
      conflict: "Cannot coexist with conclusion CTA."
    },
    {
      name: "Lessons / Takeaways Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires above 1,200 words when post explicitly offers transferable lessons. Must come from user-provided substance.",
      conflict: "None"
    },
    {
      name: "Data / Research Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when user provides stats or original data, or third-party data is available. Brand's own internal data is always allowed. [F.0.3 applies for third-party data]",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic affects an identifiable industry community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Editor's Note / Context Insert",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when post references an event or term host platform's audience may not have. One sentence, bracketed.",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it. Drops when anonymous route is selected.",
      conflict: "None"
    }
  ]
}
```

---

### F.10 — SEO-Focused Article

```
TEMPLATE = {
  sub_type: "seo-article",
  max_sections: 8,

  skeletons: {
    "Skeleton A (Informational)": [
      "Hook",
      "Definition / What Is It",
      "Why It Matters",
      "Core Explanation Sections",
      "Conclusion with Next Step"
    ],
    "Skeleton B (Commercial Investigation)": [
      "Hook",
      "Quick Answer",
      "Evaluation Criteria",
      "Options / Breakdown Sections",
      "Recommendation",
      "CTA"
    ],
    "Skeleton C (Mixed / Unclear)": [
      "Hook",
      "Definition",
      "Core Argument",
      "Supporting Sections",
      "Conclusion"
    ]
  },

  components: [
    {
      name: "Author Credentials Block",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Mandatory for YMYL sectors. Supporting for all others. User must provide.",
      conflict: "None"
    },
    {
      name: "Definition / What Is Block",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory for Skeleton A. Fires when keyword is a concept requiring definition.",
      conflict: "None"
    },
    {
      name: "Evaluation Criteria Block",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory for Skeleton B. Defines criteria before evaluation begins.",
      conflict: "None"
    },
    {
      name: "Data / Statistics Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when user provides original data or credible third-party stats are available. Named sources only. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Expert Quote Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires above 1,400 words. Only when a credible named source is clearly available. Skip if source is unclear. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Internal Link Block",
      tier: "Supporting",
      type: "Structural",
      activation: "Always suggested. Minimum 2 placeholders.",
      conflict: "None"
    },
    {
      name: "FAQ Section",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when questions exist beyond those already covered as H2 sections.",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when topic affects a specific identifiable community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Mandatory for YMYL sectors (escalates to Conditional Core). Otherwise fires when user provides it.",
      conflict: "None"
    }
  ]
}
```

---

### F.11 — Beginner's Guide

```
TEMPLATE = {
  sub_type: "beginners-guide",
  max_sections: 8,

  core_sections: [
    "Motivation Block",
    "Expectation Setting Block",
    "Foundational Definition Block",
    "Progressive Instructional Sections",
    "Challenges / Obstacles Block",
    "First Steps",
    "Next Steps / Resources Block"
  ],

  components: [
    {
      name: "Motivation Block",
      tier: "Core",
      type: "Narrative",
      activation: "Always fires. Never fabricated.",
      conflict: "None"
    },
    {
      name: "Expectation Setting Block",
      tier: "Core",
      type: "Narrative",
      activation: "Always fires. Honest and specific — never vague reassurance.",
      conflict: "None"
    },
    {
      name: "Foundational Definition Block",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Defines topic in plain language before instruction begins.",
      conflict: "None"
    },
    {
      name: "Progressive Instructional Sections",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Each section assumes only what previous sections established.",
      conflict: "None"
    },
    {
      name: "Next Steps / Resources Block",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Internal links primary. External resources eligible when credible named sources are available. Never a brand push.",
      conflict: "None"
    },
    {
      name: "Table of Contents",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory above 1,300 words. Optional below. Minimum 4 sections.",
      conflict: "None"
    },
    {
      name: "Glossary / Key Terms Block",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Fires as standalone section when topic requires 4+ foundational terms. Fires inline when fewer than 4. Critical terms always defined prominently regardless of count.",
      conflict: "None"
    },
    {
      name: "Challenges / Obstacles Block",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Fires when topic has documented learning curve or known failure points. Placed before instructional sections. Drops when topic is simple enough that a challenges block would feel patronising.",
      conflict: "None"
    },
    {
      name: "Soft Mid-Guide CTA",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Fires when brand has a tool or resource that directly supports a point being made. One mention maximum. Never in challenges section or glossary.",
      conflict: "Cannot coexist with aggressive conclusion CTA."
    },
    {
      name: "Quick Win Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires only when topic allows a meaningful immediate action — not for purely conceptual or theoretical topics. Placed after expectation setting.",
      conflict: "None"
    },
    {
      name: "Mental Model Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic involves multiple interacting concepts. Provides a simple framework to mentally organise information.",
      conflict: "None"
    },
    {
      name: "Common Misconceptions Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic has widely held beginner misconceptions. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Expert / Community Quote Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires above 1,400 words. Named credible sources only. Never fabricated. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic affects a specific identifiable community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when user provides it or explicitly requests it.",
      conflict: "None"
    }
  ]
}
```

---

### F.12 — Ultimate Guide / Pillar Post

```
TEMPLATE = {
  sub_type: "ultimate-guide",
  max_sections: 10,
  section_minimum: 5,

  note: "Scope check fires before any generation. Section ceiling is advisory above 10 — not a hard block. Internal links in F.12 are primarily incoming. Outgoing links placed only for topics genuinely outside this guide's scope.",

  core_sections: [
    "Table of Contents",
    "Roadmap Block",
    "Foundational Definition Section",
    "Why This Matters Section",
    "Core Concept Sections",
    "Subsection Rules Application",
    "Practical Application Section",
    "Common Mistakes Section",
    "Tools and Resources Section",
    "Next Steps"
  ],

  components: [
    {
      name: "Table of Contents",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Minimum 5 sections.",
      conflict: "None"
    },
    {
      name: "Roadmap Block",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Fires when guide has 5+ major sections. Placed between TOC and first section. Navigation tool, not summary.",
      conflict: "None"
    },
    {
      name: "Foundational Definition Section",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Defines topic precisely. Sets scope boundary — what this guide covers and what it doesn't.",
      conflict: "None"
    },
    {
      name: "Why This Matters Section",
      tier: "Core",
      type: "Narrative",
      activation: "Always fires. Establishes stakes before depth begins.",
      conflict: "None"
    },
    {
      name: "Core Concept Sections",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. Each section covers one major dimension exhaustively.",
      conflict: "None"
    },
    {
      name: "Subsection Rules Application",
      tier: "Core",
      type: "Structural",
      activation: "Always fires. When a subsection is best structured as a How-to — How-to rules apply. When best as listicle — listicle rules apply. F.12 orchestrates other subtypes as internal tools.",
      conflict: "None"
    },
    {
      name: "Practical Application Section",
      tier: "Conditional Core",
      type: "Structural",
      activation: "Mandatory when topic has actionable dimensions. Bridges theory and practice. Drops only for purely theoretical topics.",
      conflict: "None"
    },
    {
      name: "Author Credentials Block",
      tier: "Conditional Core",
      type: "Narrative",
      activation: "Mandatory for YMYL sectors. Supporting for all others. User must provide.",
      conflict: "None"
    },
    {
      name: "Concept Framework Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic involves multiple interacting concepts or dimensions. Provides structural model of the topic.",
      conflict: "None"
    },
    {
      name: "Data and Research Section",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when user provides original data or credible third-party stats are available. Named sources only. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Case Study or Example Section",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires when topic benefits from real-world proof. [F.0.3 applies for third-party examples]",
      conflict: "None"
    },
    {
      name: "Common Mistakes Section",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires above 3,000 words. Specific documented failure modes with causes and fixes. Vague warnings banned.",
      conflict: "None"
    },
    {
      name: "Tools and Resources Section",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when topic involves specific tools the reader needs. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "FAQ Section",
      tier: "Supporting",
      type: "Structural",
      activation: "Fires when questions exist beyond those already covered as major sections.",
      conflict: "If Core Concept Sections already cover the questions — reduce to remaining only."
    },
    {
      name: "Expert Quote Block",
      tier: "Supporting",
      type: "Narrative",
      activation: "Fires above 3,000 words. Named credible sources only. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Community Relevance Block",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Fires when topic affects a specific identifiable community. [F.0.3 applies]",
      conflict: "None"
    },
    {
      name: "Author / Brand Bio",
      tier: "Enrichment",
      type: "Narrative",
      activation: "Mandatory for YMYL sectors (escalates to Conditional Core). Otherwise fires when user provides it.",
      conflict: "None"
    }
  ]
}
```

---

## Output Format

The blog-templates skill returns a structured template object consumed by `blog/SKILL.md`. The object is identical for a given `sub_type` on every call.

```
TEMPLATE = {
  sub_type: "",           // canonical sub_type value from CONTENT_BRIEF
  max_sections: "",       // H2 ceiling for this subtype
  core_sections: [],      // ordered list of core structural section names
  components: [           // full component list with metadata
    {
      name: "",
      tier: "",           // Core | Conditional Core | Supporting | Enrichment
      type: "",           // Structural | Narrative
      activation: "",     // condition that fires the component
      conflict: ""        // coexistence rules — "None" if no conflict
    }
  ]
}
```

The generation skill uses this object to:

1. Establish the H2 structure before drafting begins
2. Identify which Core components fire unconditionally
3. Evaluate activation signals for Conditional Core components
4. Apply the supporting component limit based on `length_tier`
5. Include Enrichment components only when explicitly available in the brief

---

## Prohibited Behaviour

The blog-templates skill must never:

- Generate blog text, sentences, or paragraphs
- Alter section names or component wording dynamically based on topic
- Modify `CONTENT_BRIEF` in any field
- Trigger intake questions
- Perform SEO analysis or keyword checks
- Run web search
- Introduce components not defined in this skill
- Decide which supporting or enrichment components activate — activation is owned by the generation skill
- Return different structures for the same `sub_type` on repeated calls
