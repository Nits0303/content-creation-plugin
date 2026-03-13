# Hospitality / Travel Sector Overlay

**Sector:** Hospitality / Travel
**CONTENT_BRIEF.sector value:** `hospitality-travel`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = hospitality-travel`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility expectations, tone, destination specificity, and amenity and service description accuracy for Hospitality / Travel content.

---

## Sector Scope

This overlay applies when the content subject falls within the Hospitality / Travel sector. Typical signals include:

- Hotels, resorts, and accommodation providers
- Travel destinations and tourism experiences
- Vacation planning and itinerary content
- Hospitality services and guest experience
- Travel guides and destination overviews
- Short-stay, boutique, and luxury accommodation
- Activities, attractions, and local experiences

Common terminology signals: hotel, resort, travel, tourism, destination, vacation, accommodation, guest, hospitality, booking, stay, trip.

**YMYL classification:** Non-YMYL for general travel and destination content. Content involving significant financial commitments (package bookings, high-value travel), health or safety considerations (travel restrictions, vaccination requirements), or regulatory matters may carry elevated responsibility expectations.

---

## Destination Specificity

Travel and hospitality content is inherently location-dependent. Experiences, amenities, attractions, and conditions vary significantly by destination. Generation skills may reference this guidance when handling location context in this sector:

- Travel experiences differ meaningfully by destination — general statements about travel quality or conditions do not transfer across locations
- Amenities and attractions are specific to particular properties, cities, regions, and countries — they should not be implied or generalised without geographic grounding
- Climate, seasonal patterns, and local conditions affect travel experiences and should be treated as destination-specific rather than assumed to be universal

Content in this sector often benefits from clear geographic anchoring:

- City, town, or specific neighbourhood or district
- Region or country
- Property or venue name when relevant
- Market segment (budget, mid-range, luxury, adventure, family, etc.)

Avoid generic travel descriptions that could apply to any destination without modification.

---

## Terminology Guidance

Hospitality and travel content uses industry-specific terminology related to accommodation, services, and travel planning. Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Explain travel and hospitality terminology clearly on first use
- Avoid unexplained industry jargon (rack rate, concierge, pax, MICE, OTA, etc.) — define before using
- Emphasise practical travel understanding over industry-insider framing
- Assume the reader may be unfamiliar with booking processes, accommodation classifications, or destination-specific conventions

**Intermediate**
- Assume basic familiarity with common travel concepts and accommodation types
- Reduce introductory explanations for widely understood terminology
- Emphasise destination detail, experience quality, and practical planning information
- Use standard hospitality and travel vocabulary accurately without over-explaining

**Advanced**
- Precise hospitality and tourism industry terminology may be used without simplification
- Assume familiarity with travel planning concepts, accommodation grading systems, and destination research conventions
- Prioritise depth and specificity in destination or service descriptions

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Experience-Focused Content

Hospitality and travel content often focuses on experiences rather than tangible products. The primary job of content in this sector is frequently to help readers understand what an experience involves and whether it suits their needs or interests.

Generation skills may reference the following expectations when approaching experience-focused content in this sector:

- Describe what the experience involves — what the traveller or guest actually does, sees, or feels
- Explain what makes the destination, property, or service appealing — and to whom
- Connect amenities, attractions, and services to the traveller's likely experience rather than listing them in isolation
- Acknowledge variability where relevant — experiences can differ by season, group type, budget, or personal preference

Avoid descriptions that rely entirely on promotional adjectives without explaining the substance of the experience. "Breathtaking views" and "world-class service" communicate nothing specific — the underlying detail is what builds credibility and interest.

---

## Credibility Expectations

Travel audiences expect realistic, grounded descriptions of destinations and hospitality services. Credibility in this sector is built through accurate specificity and honest representation of what a destination or property offers. Generation skills may reference the following guidance:

- Describe amenities accurately — do not imply facilities, services, or features that are not confirmed in the brief or context material
- Represent destinations realistically — acknowledge trade-offs, limitations, or conditions where relevant rather than presenting only the promotional view
- Avoid presenting hypothetical or aspirational travel experiences as guaranteed outcomes
- Avoid exaggerating destination or property qualities beyond what the source material supports
- Travel advice should be practically useful — overstated descriptions set expectations that the actual experience may not meet, which damages trust

The standard to hold is: would a seasoned traveller or a first-time visitor to this destination find this accurate and useful, or would they find it generic, exaggerated, or misleading?

---

## Tone Calibration

The default tone for Hospitality / Travel content balances inspiration, clarity, and practical travel information. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Descriptive clarity about experiences, settings, and amenities that helps the reader form a realistic picture
- Balanced travel inspiration supported by specific, factual detail
- Practical explanations of what travellers need to know about a destination, property, or experience
- Evocative but grounded language — specificity creates more genuine appeal than generic enthusiasm

**Avoid:**
- Exaggerated travel promises that set expectations the experience cannot meet
- Generic destination hype that could describe any location ("vibrant", "magical", "unforgettable" as standalone claims without grounding)
- Unrealistic traveller expectations about cost, comfort, or experience quality
- Urgency tactics that pressure travel or booking decisions

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, aspirational luxury travel tone for a high-end resort), generation skills can follow the brief's tone while maintaining accuracy and avoiding unverified claims within that register.

---

## Amenity and Service Description

Descriptions of hospitality amenities and travel services benefit from clear, experience-connected explanation. Generation skills may reference the following pattern when handling amenity and service content in this sector:

1. **What is available** — describe the amenity or service concretely
2. **How it improves the experience** — explain the practical or experiential benefit to the guest or traveller
3. **What to expect realistically** — provide an honest framing of what the amenity or service delivers

**Avoid:**
- Listing amenities without explaining their relevance or appeal to the target traveller
- Implying services or facilities that are not confirmed in the brief or context material
- Exaggerating hospitality features — overstated amenity descriptions create mismatched expectations

When `CONTENT_BRIEF.content_points` references specific amenities, services, or destination features, generation skills can follow this explanatory pattern for each point.

---

## Disclaimer Awareness

Some travel content requires disclaimers, particularly when content touches on pricing, availability, restrictions, or booking conditions. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- References travel costs, accommodation pricing, or package pricing
- Describes seasonal availability or time-limited offers
- Discusses travel restrictions, visa requirements, or entry conditions
- Explains booking terms, cancellation policies, or deposit conditions

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Travel and hospitality content carries hallucination risk in several specific areas. Generation skills may reference this guidance when applying source discipline in this sector:

- **Hotel and property amenities** — do not invent facilities, services, or features for named properties not confirmed in the brief or context material
- **Destination attractions** — do not fabricate activities, landmarks, or experiences as being available at a specific destination unless confirmed
- **Travel costs and pricing** — do not invent accommodation rates, transport costs, or package pricing
- **Travel restrictions and entry requirements** — do not assert visa rules, vaccination requirements, or border policies unless confirmed in the source material
- **Booking and cancellation policies** — do not fabricate terms, conditions, or refund policies for specific providers
- **Seasonal and operational details** — do not assert opening periods, operating hours, or availability windows that have not been confirmed

All factual claims about destinations, properties, and travel services should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe the destination category or travel experience type in general terms rather than asserting specific property or destination facts that have not been confirmed.
