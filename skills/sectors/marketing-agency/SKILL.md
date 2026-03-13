# Marketing Agency Sector Overlay

**Sector:** Marketing Agency
**CONTENT_BRIEF.sector value:** `marketing-agency`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = marketing-agency`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility expectations, tone, campaign results framing, and marketing claim discipline for Marketing Agency content.

---

## Sector Scope

This overlay applies when the content subject falls within the Marketing Agency sector. Typical signals include:

- Digital marketing agencies and full-service advertising agencies
- SEO and content marketing service providers
- Social media marketing and community management services
- Paid advertising and PPC campaign management
- Marketing automation platforms and services
- Lead generation and demand generation services
- Branding, creative, and design agencies

Common terminology signals: marketing, campaign, advertising, SEO, lead generation, conversion, analytics, brand strategy, content marketing, PPC, ROI, engagement.

**YMYL classification:** Non-YMYL for general marketing content. Content involving significant financial commitments, investment in marketing services, or regulatory advertising obligations may carry elevated responsibility expectations.

---

## Marketing Terminology

Marketing content frequently uses specialised terminology and metric-based language that should be applied accurately. Imprecise use of marketing terms, acronyms, or performance metrics can undermine credibility with informed audiences.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Explain marketing terminology clearly on first use
- Avoid unexplained acronyms — CTR, CPC, ROAS, CPL, CPA, MQL, SQL, and similar shorthand should be defined before use
- Emphasise practical marketing outcomes and what concepts mean for the reader's business rather than technical precision
- Assume the reader may be unfamiliar with campaign structures, attribution models, or platform-specific mechanics

**Intermediate**
- Assume basic familiarity with common marketing concepts and digital channels
- Reduce introductory explanations for widely understood terminology
- Focus on strategy, performance interpretation, and practical application
- Use standard marketing vocabulary accurately without over-explaining

**Advanced**
- Precise marketing, analytics, and campaign management terminology may be used without simplification
- Assume familiarity with campaign metrics, attribution frameworks, funnel structures, and platform-specific mechanics
- Prioritise depth and analytical specificity over accessibility scaffolding

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Results Claim Discipline

Marketing content frequently discusses campaign performance, outcomes, and service value. This is an area of elevated claim risk — marketing audiences are often sophisticated enough to recognise exaggerated or unsubstantiated performance claims. Generation skills may reference the following guidance when handling results and outcome claims:

- Marketing outcomes should not be presented as guaranteed — results depend on audience, industry, budget, competitive context, and execution quality
- Performance claims should be described with appropriate context rather than as universal expectations
- Avoid claims such as "guaranteed leads", "instant traffic growth", "automatic ROI", or "10x results" without specific qualifying context
- Campaign performance discussions should emphasise the strategy, execution process, and optimisation approach rather than promising fixed outcomes

When `CONTENT_BRIEF.content_points` includes performance claims or case study results, generation skills can reference this discipline when working with those points.

---

## Credibility Expectations

Marketing audiences — whether clients, prospects, or industry peers — expect grounded, credible representation of services and results. Credibility in this sector is built through process clarity, honest performance framing, and accurate case study representation. Generation skills may reference the following guidance:

- Campaign and service descriptions should be grounded in real, recognisable marketing processes rather than vague capability claims
- Case study content should avoid fabricated or inflated metrics — invented results damage credibility when scrutinised by informed readers
- Performance discussions should remain realistic — acknowledge that results vary rather than presenting outlier outcomes as typical
- Avoid implying success rates, conversion benchmarks, or growth figures that are not confirmed in the brief or context material

The standard to hold is: would an experienced marketing professional find this credible and process-grounded, or would they find it promotional, vague, or implausible?

---

## Tone Calibration

The default tone for Marketing Agency content balances expertise, clarity, and confidence. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Confident explanation of marketing strategy and methodology grounded in specific process detail
- Clear articulation of service value through what the agency does and how it creates outcomes
- Practical, informed discussion of marketing methods that demonstrates sector knowledge
- Authoritative voice that reflects genuine expertise rather than enthusiasm

**Avoid:**
- Exaggerated hype-driven marketing language applied to the agency's own services ("we transform businesses", "unstoppable growth", "dominate your market")
- Unrealistic growth promises or implied guarantees about campaign outcomes
- Manipulative or high-pressure persuasion tactics that undermine professional credibility
- Vague marketing buzzwords used without substantive explanation ("data-driven", "innovative", "holistic" as standalone claims)

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, a bold brand voice for a high-energy creative agency), generation skills can follow the brief's tone while maintaining responsible framing and avoiding unverified claims within that register.

---

## Case Study and Campaign Explanation

Marketing content frequently requires explaining campaigns, strategies, and outcomes. Generation skills may reference the following pattern when constructing campaign or case study content in this sector:

1. **The marketing challenge** — describe the problem, objective, or brief the campaign was designed to address
2. **The strategy used** — explain the approach, channel selection, and reasoning behind the marketing decisions
3. **The implementation** — outline how the strategy was executed, including key activities and optimisation steps

**Avoid:**
- Presenting unverified or fabricated campaign metrics as evidence of performance
- Implying specific results (conversion rates, lead volumes, revenue uplift) that are not confirmed in the brief or context material
- Using vague marketing buzzwords without explaining the actual strategy or process they describe
- Attributing campaign success to a single tactic without acknowledging the role of execution, testing, and context

When `CONTENT_BRIEF.content_points` references specific campaigns, case studies, or strategy explanations, generation skills can follow this pattern for each point.

---

## Disclaimer Awareness

Some marketing content requires disclaimers, particularly when content discusses campaign performance results, ROI expectations, or marketing benchmarks. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- States or implies specific campaign performance results or ROI figures
- Describes marketing benchmarks or industry average performance metrics
- References advertising platform policies or compliance requirements
- Involves financial commitments associated with marketing service engagement

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Marketing content carries hallucination risk in several specific areas. Generation skills may reference this guidance when applying source discipline in this sector:

- **Campaign performance metrics** — do not invent conversion rates, CTR, CPC, ROAS, lead volumes, or revenue outcomes not confirmed in the brief or context material
- **Marketing statistics** — do not fabricate industry benchmarks, average performance figures, or sector-specific data
- **Platform policies and advertising rules** — do not assert advertising platform policies, targeting capabilities, or compliance requirements unless confirmed in the source material
- **Case study results** — do not invent client outcomes, before-and-after figures, or attributed campaign results
- **Tool or platform capabilities** — do not fabricate features, integrations, or performance characteristics of named marketing platforms or tools

All factual claims about campaign performance, marketing metrics, and platform capabilities should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe general marketing practice or channel behaviour rather than asserting specific performance figures or platform facts that have not been confirmed.
