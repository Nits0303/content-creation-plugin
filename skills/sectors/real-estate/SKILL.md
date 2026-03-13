# Real Estate Sector Overlay

**Sector:** Real Estate
**CONTENT_BRIEF.sector value:** `real-estate`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = real-estate`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility expectations, tone, geographic specificity, and property description accuracy for Real Estate content.

---

## Sector Scope

This overlay applies when the content subject falls within the Real Estate sector. Typical signals include:

- Residential property sales and purchases
- Commercial real estate and mixed-use developments
- Property investment and portfolio management
- Property development and construction projects
- Real estate brokerage and agency services
- Rental markets and leasing
- Housing trends and market analysis
- Property buying and selling processes

Common terminology signals: property, real estate, housing, broker, agent, listing, developer, RERA, property investment, rental market, commercial property, residential property.

**YMYL classification:** Partial. Real estate content involving financial investment decisions, regulatory obligations, or significant purchase guidance carries elevated responsibility expectations. Standard generation rules apply for general informational content.Content addressing investment returns, financial commitments, or regulatory requirements typically requires heightened claim discipline.
---

## Geographic Specificity

Real estate markets are inherently local. Property conditions, pricing, regulations, and processes vary significantly across cities, regions, and neighbourhoods. Generation skills may reference this guidance when handling location context in this sector:

- Property markets differ significantly by location — general statements about property trends may not hold across regions
- Regulatory frameworks, ownership rules, and property processes differ across jurisdictions; content should reflect the relevant geography rather than apply universal assumptions
- Property pricing and yield expectations are location dependent and should not be generalised without geographic qualification

Content in this sector often benefits from clear geographic anchoring:

- City or metropolitan area
- Region, state, or jurisdiction
- Neighbourhood or locality
- Market segment (luxury, affordable housing, commercial district, etc.)

Avoid generic property descriptions that imply conditions are consistent across locations when the topic is geographically specific.

---

## Terminology Guidance

Real estate content uses specialised terminology that should be applied accurately. Casual or imprecise use of property terms can mislead readers and undermine credibility.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Define property and real estate terms clearly on first use
- Distinguish between buying, selling, leasing, and investing — these involve different processes and obligations
- Explain industry shorthand (stamp duty, carpet area, built-up area, RERA, yield, cap rate, etc.) before using it
- Avoid assuming familiarity with regulatory terminology or financial instruments specific to property transactions

**Intermediate**
- Assume basic familiarity with common property transaction concepts
- Reduce or omit definitions for widely understood terminology
- Retain precision — distinguish clearly between property types (residential, commercial, mixed-use) and transaction types (freehold, leasehold, joint development, etc.)

**Advanced**
- Precise industry and regulatory terminology may be used without oversimplification
- Assume familiarity with investment structures, development frameworks, and regulatory bodies such as RERA
- Avoid explaining foundational concepts that an experienced property professional or investor would consider basic

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Credibility Expectations

Real estate audiences expect grounded, realistic information. Credibility in this sector is built through concrete detail, accurate market framing, and responsible presentation of investment or financial context. Generation skills may reference the following guidance:

- Property descriptions should be concrete and specific rather than purely promotional — what a property has and how it functions matters more than adjective-heavy enthusiasm
- Property investment discussions should avoid exaggerated profit claims or implied guarantees about returns
- Market trend discussions should be framed responsibly — trends reflect conditions at a point in time and are subject to change
- Avoid unrealistic pricing expectations or implied certainty about future valuations
- Regulatory references (RERA, local planning authorities, stamp duty rules) should be accurate and jurisdiction-specific

The standard to hold is: would a property buyer, investor, or agent find this credible and useful, or would they find it vague, overstated, or out of step with market reality?

---

## Tone Calibration

The default tone for Real Estate content balances professionalism, clarity, and practicality. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Descriptive clarity when explaining property features or market conditions
- Practical, process-oriented explanations for buying, selling, or leasing
- Realistic framing of investment potential and market conditions
- Confidence grounded in specific, verifiable detail

**Avoid:**
- Exaggerated investment promises ("guaranteed returns", "always appreciates")
- Hype-driven property descriptions that substitute adjectives for substance ("stunning", "unmissable", "once-in-a-lifetime opportunity")
- Urgency tactics that pressure financial decisions ("act now before prices rise further")
- Generic enthusiasm that could apply to any property in any location

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, aspirational for a luxury property campaign), generation skills can follow the brief's tone while maintaining responsible and accurate framing within that register.

---

## Property Description Guidance

Property descriptions and real estate content frequently require explaining what a property is, how it functions, and why it suits a particular use or audience. Generation skills may reference the following guidance when handling property-specific content:

**Favour:**
- Concrete descriptions of property features — size, configuration, condition, facilities, and location attributes
- Explanations of how a property fits a specific use case (family residence, rental investment, commercial occupancy, development site)
- Location context that explains what the surrounding area offers — transport, amenities, demand drivers
- Honest framing of trade-offs where relevant — proximity to amenities versus noise, size versus price, etc.

**Avoid:**
- Vague property descriptions that rely entirely on adjectives without substantive detail
- Listing amenities or features without explaining their practical value to the intended audience
- Implied perfection — responsible property content acknowledges relevant limitations alongside strengths

When `CONTENT_BRIEF.content_points` references specific property features, locations, or investment attributes, generation skills can follow this explanatory pattern for each point.

---

## Disclaimer Awareness

Some real estate content requires disclaimers, particularly when content touches on investment expectations, financial commitments, or regulatory obligations. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- Discusses investment returns, rental yields, or capital appreciation
- Explains regulatory requirements (RERA compliance, stamp duty, tax obligations)
- Addresses financial decisions associated with property purchase or development

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Real estate content carries elevated hallucination risk in several areas. Generation skills may reference this guidance when applying source discipline in this sector:

- **Property prices** — do not invent specific property valuations, price per square foot figures, or transaction prices not confirmed in the brief or context material
- **Market statistics** — do not fabricate market trend data, absorption rates, inventory figures, or demand indices
- **Regulatory requirements** — do not invent jurisdiction-specific rules, RERA obligations, planning permissions, or stamp duty rates
- **Development approvals** — do not assert that a development has received regulatory or planning approval unless confirmed in the source material
- **Investment returns** — do not fabricate rental yields, capital appreciation rates, or ROI projections not provided by the user
- **Comparable transactions** — do not invent recent sales or rental transactions as supporting evidence

All factual claims about property markets, pricing, and regulation should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe general market dynamics or property category behaviour rather than asserting specific figures or regulatory facts that have not been confirmed.
