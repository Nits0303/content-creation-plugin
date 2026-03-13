# skills/webpage-templates/SKILL.md

## Skill Purpose

This skill is a template registry for the webpage generation skill. It receives a CONTENT_BRIEF, reads `CONTENT_BRIEF.sub_type`, loads the matching template definition, and returns a deterministic section skeleton.

Templates define structural skeletons only. They specify H2 section structures, component tiers, activation signals, and conflict rules. They do not generate prose, define tone, perform SEO decisions, include humanise instructions, or contain runtime logic.

---

## Responsibilities

- Receive CONTENT_BRIEF from `webpage/SKILL.md`
- Read `CONTENT_BRIEF.sub_type`
- Match sub_type to the correct template in this registry
- Return the section skeleton and component library for that subtype
- Enforce section ceiling for the matched subtype

---

## Non-Responsibilities

This skill must NOT:

- Ask questions
- Modify CONTENT_BRIEF
- Generate page copy or prose
- Apply tone rules
- Perform SEO logic
- Run sector overlays
- Run humanise passes
- Define writing standards
- Apply hallucination safeguards (owned by generation skill)

---

## Template Selection Logic

```
RECEIVE CONTENT_BRIEF
READ CONTENT_BRIEF.sub_type

MATCH sub_type TO template:

  "homepage"             → Template I.A.1
  "landing-page"         → Template I.B.1
  "sales-page"           → Template I.B.2
  "product-page"         → Template I.C.1
  "services-page"        → Template I.C.2
  "pricing-page"         → Template I.C.3
  "industry-page"        → Template I.C.4
  "testimonials-page"    → Template I.D.1
  "case-study-page"      → Template I.D.2
  "portfolio-page"       → Template I.D.3
  "about-page"           → Template I.E.1
  "faq-page"             → Template I.E.2
  "privacy-terms-page"   → Template I.E.3
  "contact-page"         → Template I.F.1
  "404-page"             → Template I.F.2
  "thank-you-page"       → Template I.F.3

  NO MATCH → return error to webpage/SKILL.md: "sub_type not recognised"

RETURN section skeleton + component library
```

---

## Webpage Template Library

---

### FAMILY 1: ORIENTATION PAGES

---

#### Template I.A.1 — Homepage

**Section Ceiling:** 7 sections hard maximum

**Locked Section Order:**
1. Hero
2. Social Proof Snapshot
3. Product / Service Overview
4. Navigation Pathways
5. Supporting Explanation or Differentiator
6. Secondary Trust Element or Use Cases
7. Closing CTA

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Hero Block | Core | Structural | Always fires | None |
| Social Proof Snapshot | Core | Structural | Always fires. Must appear within first three sections | Conflicts with full Testimonials Block — snippets and logos only, never full quotes |
| Product / Service Overview Layer | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.topic` | None |
| CTA Architecture | Core | Structural | Always fires. One primary CTA in hero, one secondary lower | Two distinct CTA types maximum. Third CTA type — consolidate first |
| Navigation Pathway Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains two or more distinct visitor type entries. Single entry → block does not fire. Four or more entries → consolidate to 3 maximum | Conflicts with full navigation menus — pathways only, not site-wide nav |
| Brand Differentiation Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains a differentiator entry | None |

---

### FAMILY 2: CONVERSION PAGES

---

#### Template I.B.1 — Landing Page

**Section Ceiling:** 7 sections hard maximum

**Structural Notes:** Single-offer focused. No outbound navigation links. Single CTA type throughout.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Single-Goal Constraint | Core | Structural | Always fires. One CTA type. Zero navigation links | None |
| Problem-Agitation Block | Core | Structural | Always fires. Visitor pain stated before offer appears | None |
| Benefit Stack | Core | Structural | Always fires. 3–5 benefits maximum before first CTA | Conflicts with feature list — outcomes only, not features |
| Objection Handling Block | Core | Structural | Always fires. 3–5 specific objections answered inline | Conflicts with FAQ Page format — inline placement only |
| Traffic Source Calibration | Conditional Core | Narrative | Fires when `CONTENT_BRIEF.content_points` contains a traffic source entry. Entry value `cold` → more context depth. Entry value `warm` → less. No traffic source entry → defaults to cold | None |
| Urgency / Incentive Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains an urgency signal entry. Absent → block does not fire | Absent entry means absent block |
| Brand Differentiation Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains a differentiator entry | None |

---

#### Template I.B.2 — Sales Page

**Section Ceiling:** 9 sections advisory — 14 sections absolute consolidation threshold

**Structural Notes:** No outbound navigation links. Minimum 3 CTA placements.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Pain Amplification Section | Core | Structural | Always fires | None |
| Transformation Promise | Core | Structural | Always fires. Before state vs after state | None |
| Full Offer Breakdown | Core | Structural | Always fires. Price, inclusions, bonuses, delivery | None |
| Objection Handling Block | Core | Structural | Always fires. Inline — appears where objections naturally arise in reading flow | Conflicts with standalone FAQ block — inline placement required |
| Multiple CTA Placements | Core | Structural | Always fires. Minimum 3 placements: after transformation promise, after proof section, after guarantee | Minimum 3 placements |
| Price Anchoring Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a price entry | None |
| Guarantee Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a guarantee entry | None |
| Comparison / Positioning Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains a competitor positioning entry | None |

---

### FAMILY 3: EVALUATION PAGES

---

#### Template I.C.1 — Product Page

**Section Ceiling:** 7 sections hard maximum

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Feature-Benefit Matrix | Core | Structural | Always fires. Each feature paired with its outcome | None |
| Screenshot / Demo Gallery | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a visual or interface description AND `CONTENT_BRIEF.brand_inclusions` contains one or more asset entries | Conflicts with Technical Specs Block when both would dominate — Feature-Benefit Matrix takes precedence |
| Technical Specs Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.sector` is `it-saas`, `hardware`, or `manufacturing-b2b` AND `CONTENT_BRIEF.visitor_familiarity` is `solution-aware` or `product-aware` | Fires only for the named sectors |
| Integration Ecosystem Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.third_party_mentions` is populated | None |
| Time-to-Value Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.sector` is `it-saas` | None |
| Ideal Fit Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.length_tier` is `medium` or `long`. Both who it is for and who it is not — both sides present when it fires | None |

---

#### Template I.C.2 — Services Page

**Section Ceiling:** 7 sections hard maximum

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Service Breakdown Cards | Core | Structural | Always fires. One card per service. Each card: what it is, who it is for, what the deliverable is | None |
| Process / Methodology Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a methodology entry | None |
| Pricing Structure Hint | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a pricing display entry. Three states: ranges / starting price / consultation only | None |
| Ideal Client Profile Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.length_tier` is `medium` or `long`. Both who the service is for and who it is not — both sides present when it fires | None |
| Brand Differentiation Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains a differentiator entry | None |

---

#### Template I.C.3 — Pricing Page

**Section Ceiling:** 7 sections hard maximum

**Structural Notes:** Clarity register — structure must support scanning, not narrative reading.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Plan Comparison Table | Core | Structural | Always fires. Tiers side by side with feature availability | None |
| Most Popular Plan Highlight | Conditional Core | Structural | Fires when `CONTENT_BRIEF.recommended_plan` is populated | None |
| Annual / Monthly Toggle Logic | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a billing options entry specifying both monthly and annual | None |
| Free Trial / Refund Policy Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a trial or refund policy entry | None |
| Billing FAQ Block | Supporting | Structural | Fires when `CONTENT_BRIEF.content_points` contains 3 or more plan entries OR a usage-based or enterprise pricing entry | None |

---

#### Template I.C.4 — Industry Page

**Section Ceiling:** 7 sections hard maximum

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Industry-Specific Pain Points | Core | Structural | Always fires. Named for the target vertical in `CONTENT_BRIEF.topic` | None |
| Use Case Scenarios | Core | Structural | Always fires. 2–3 specific situations drawn from `CONTENT_BRIEF.content_points`. Named roles, named problems | None |
| Industry Terminology Calibration | Core | Narrative | Always fires. Applied throughout — not a standalone section. Reads from `CONTENT_BRIEF.topic` | Not a standalone section — applied at generation level |
| Industry-Specific Social Proof Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` contains industry-matched proof entries | Cross-industry proof does not activate this block |
| Industry Feature Mapping | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains one or more industry-specific feature entries | None |
| Brand Differentiation Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains a differentiator entry | None |

---

### FAMILY 4: PROOF PAGES

---

#### Template I.D.1 — Testimonials Page

**Section Ceiling:** 7 sections hard maximum

**Structural Notes:** No persuasive framing around quotes. Introductory sentences are neutral, never amplified.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Quote and Attribution Block | Core | Structural | Always fires. Attribution floor: name + company minimum. Name-only entries are excluded | None |
| Video Testimonial Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` contains video testimonial entries | None |
| Industry / Use Case Filter Logic | Supporting | Structural | Fires when `CONTENT_BRIEF.content_points` contains two or more distinct testimonial segment entries. Minimum 2 items per cluster | None |
| Review Source Aggregation Block | Supporting | Structural | Fires when `CONTENT_BRIEF.third_party_mentions` contains review platform entries. Attributed to named platform only | None |
| Testimonial Credibility Block | Supporting | Structural | Fires when `CONTENT_BRIEF.brand_inclusions` contains logo or photo asset entries | None |

---

#### Template I.D.2 — Case Study Page

**Section Ceiling:** 7 sections hard maximum

**Structural Notes:** Every metric, result, and client detail reads from `CONTENT_BRIEF`. Anonymisation state reads from `CONTENT_BRIEF.client_disclosure`.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Metrics Highlight Bar | Core | Structural | Always fires. Key result numbers surfaced before body content. `CONTENT_BRIEF.content_points` metrics entry absent → proceeds as testimonial-style case study without metrics bar | None |
| Client Profile Snapshot | Core | Structural | Always fires. 2 lines maximum. Appears before narrative body | None |
| Challenge-Solution-Results Structure | Core | Structural | Always fires. Three-part structure compressed for web format | None |
| Implementation Timeline Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains a timeline entry | None |
| PDF Download CTA | Supporting | Structural | Fires when `CONTENT_BRIEF.special_instructions` contains a PDF version entry | None |

---

#### Template I.D.3 — Portfolio Page

**Section Ceiling:** 7 sections hard maximum

**Structural Notes:** Evidence-first — no fabricated metrics, attribution untouched.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Work Sample Cards | Core | Structural | Always fires. Project name, category, brief outcome per card. Minimum viable card: name + one-line outcome | None |
| Client Disclosure | Core | Narrative | Always fires. Named or anonymised as set in `CONTENT_BRIEF.client_disclosure` | None |
| Project Detail Structure | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains featured project entries. Maximum 3 featured projects. Remaining cards stay at summary level | Maximum 3 featured projects |
| Filter / Category Logic | Supporting | Structural | Fires when `CONTENT_BRIEF.content_points` contains two or more distinct work category entries | None |
| Results Metrics Overlay | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains metrics entries. Absent → block does not fire | None |

---

### FAMILY 5: TRUST AND INFORMATION PAGES

---

#### Template I.E.1 — About Page

**Section Ceiling:** 7 sections hard maximum

**Structural Notes:** Narrative and voice are structurally appropriate here. No product selling — product mentions are incidental context only.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Mission and Values Block | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` | None |
| Origin Narrative Block | Conditional Core | Narrative | Fires when `CONTENT_BRIEF.content_points` contains an origin story entry | None |
| Team / Founder Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains team or founder entries. Individuals presented with role and relevance | None |
| Milestones and Credibility Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.content_points` contains milestone entries | None |
| Soft CTA Block | Supporting | Structural | Fires when `CONTENT_BRIEF.cta_type` is populated. Directional only — not a conversion push | Conflicts with product-selling CTAs |

---

#### Template I.E.2 — FAQ Page

**Section Ceiling:** 7 sections hard maximum

**Structural Notes:** Clarity register. Direct answer first is non-negotiable. Each cluster counts as one section.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Answer Depth Calibration | Core | Structural | Always fires. Direct answer first, elaboration after | None |
| SEO-Targeted Question Framing | Core | Narrative | Always fires. Applied to question headers only — not a standalone section | Not a standalone section — applied at generation level |
| Question Cluster Organisation | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains grouped FAQ entries with 2 or more questions per cluster | Clusters with fewer than 2 questions are flattened |
| Support Escalation Block | Supporting | Structural | Fires when `CONTENT_BRIEF.content_points` contains an escalation path entry | None |
| Link-Out Block | Supporting | Structural | Fires when `CONTENT_BRIEF.content_points` contains internal link entries | None |

---

#### Template I.E.3 — Privacy Policy / Terms Page

**Section Ceiling:** 7 sections hard maximum

**Structural Notes:** Legal register preserved. No narrative, no opinion injection, no rhythm adjustment. Last updated date mandatory at top.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Legal Section Structure | Core | Structural | Always fires. Sections: data collection, usage, cookies, third parties, user rights, contact information | None |
| Jurisdiction Declaration Block | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` for jurisdiction entry. Jurisdiction entry absent → generation does not proceed | None |
| Last Updated Date | Core | Structural | Always fires. Placed at top | None |
| Cookie and Tracking Disclosure | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a data collection entry | None |
| Plain Language Summary Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.special_instructions` contains a plain language summary entry. Summary appears first, legal text follows | None |

---

### FAMILY 6: UTILITY PAGES

---

#### Template I.F.1 — Contact Page

**Section Ceiling:** 3–4 sections hard maximum

**Structural Notes:** Function over form. Minimal friction.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Form Field Definition Block | Core | Structural | Always fires. Defines which fields appear and which are required vs optional | None |
| Department Routing Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains two or more distinct contact purpose entries. Maximum 3 routing options | Maximum 3 options |
| Location Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.content_points` contains a location entry. Address and service area both present when it fires | None |
| Response Time Expectation Block | Supporting | Narrative | Fires when `CONTENT_BRIEF.special_instructions` contains a response time entry | None |
| Alternative Contact Methods Block | Supporting | Structural | Fires when `CONTENT_BRIEF.content_points` contains one or more alternative contact channel entries | None |

---

#### Template I.F.2 — 404 Page

**Section Ceiling:** 3 sections maximum

**Structural Notes:** Navigation recovery is Core — a 404 with no way out is a dead end. No CTAs beyond navigation links.

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Error Explanation Block | Core | Structural | Always fires. 1–2 sentences maximum | None |
| Navigation Recovery Block | Core | Structural | Always fires. Homepage link plus destination pages from `CONTENT_BRIEF.content_points`. Maximum 3 destination links | Maximum 3 destination links |
| Search Block | Supporting | Structural | Fires when `CONTENT_BRIEF.special_instructions` contains a site search entry | None |

---

#### Template I.F.3 — Thank You Page

**Section Ceiling:** 3–4 sections hard maximum

**Structural Notes:** Confirmation message specificity is the trust signal. Next steps maximum 3.

**Cross-Sell Activation Rule:**
- `CONTENT_BRIEF.sector` is not `ecommerce`: Soft Cross-Sell Block is Supporting — fires when `CONTENT_BRIEF.content_points` contains a cross-sell offer entry
- `CONTENT_BRIEF.sector` is `ecommerce`: Soft Cross-Sell Block is Conditional Core — fires when `CONTENT_BRIEF.content_points` contains a cross-sell offer entry. Absent → block does not fire
- Maximum 3 items in cross-sell block regardless of sector

**Component Library:**

| Component | Tier | Type | Activation Signal | Conflict Rule |
|---|---|---|---|---|
| Confirmation Message Block | Core | Structural | Always fires. Reads from `CONTENT_BRIEF.content_points` | None |
| Next Steps Sequence Block | Core | Structural | Always fires. Maximum 3 steps. Reads from `CONTENT_BRIEF.content_points` | Maximum 3 steps |
| Onboarding Prompt Block | Conditional Core | Structural | Fires when `CONTENT_BRIEF.sector` is `it-saas` AND `CONTENT_BRIEF.content_points` contains an onboarding action entry | None |
| Soft Cross-Sell Block | Supporting / Conditional Core | Structural | See cross-sell activation rule above. One offer maximum. Maximum 3 items | Conflicts with hard conversion CTAs |

---

## Section Ceiling Reference

| Subtype | Ceiling |
|---|---|
| Homepage | 7 hard maximum |
| Landing Page | 7 hard maximum |
| Sales Page | 9 advisory / 14 absolute consolidation threshold |
| Product Page | 7 hard maximum |
| Services Page | 7 hard maximum |
| Pricing Page | 7 hard maximum |
| Industry Page | 7 hard maximum |
| Testimonials Page | 7 hard maximum |
| Case Study Page | 7 hard maximum |
| Portfolio Page | 7 hard maximum |
| About Page | 7 hard maximum |
| FAQ Page | 7 hard maximum |
| Privacy / Terms Page | 7 hard maximum |
| Contact Page | 3–4 hard maximum |
| 404 Page | 3 hard maximum |
| Thank You Page | 3–4 hard maximum |
