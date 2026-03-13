# Local Services Sector Overlay

**Sector:** Local Services
**CONTENT_BRIEF.sector value:** `local-services`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = local-services`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility expectations, tone, geographic specificity, and trust framing for Local Services content.

---

## Sector Scope

This overlay applies when the content subject falls within the Local Services sector. This sector covers businesses that provide services within a defined local geographic area. Typical examples include:

- Plumbers, electricians, and HVAC technicians
- Pest control and home repair services
- Cleaning and landscaping services
- Appliance repair and installation services
- Dentists, salons, spas, and personal care providers
- Gyms and fitness studios
- Driving schools and tutoring services

Common terminology signals: local service, service area, near me, home service, appointment, technician, repair, maintenance, service call, installation, emergency service.

**YMYL classification:** Partial. Local services content involving health and safety (medical, dental, electrical, gas, structural), significant financial commitments, or licensing and regulatory compliance carries elevated responsibility expectations. Standard generation rules apply for general service description and informational content.

---

## Geographic Specificity

Local services are geographically bounded by definition. The relevance of content in this sector depends on whether the reader is within the service area. Generation skills may reference this guidance when handling location context:

- Service areas should be described clearly where known — city, suburb, district, or postcode area help readers assess relevance
- Local context, where available, helps content serve its purpose — a service provider covering specific neighbourhoods benefits from that specificity being reflected in the content
- Generic descriptions that ignore the geographic nature of local services reduce practical usefulness to the reader

Location information must come from the user's brief or provided context material. Generation skills must not invent service locations, coverage areas, or geographic claims not confirmed in the source material.

---

## Terminology Guidance

Local services content generally targets non-expert audiences seeking practical solutions to everyday problems. Accessibility and clarity are more important in this sector than technical depth for most content types.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Explain services clearly in everyday language
- Avoid unexplained technical jargon — trade-specific terms (ballcock, circuit breaker, condenser coil, etc.) should be defined or avoided in favour of plain-language equivalents
- Focus on practical problem–solution framing that the reader can immediately relate to their situation

**Intermediate**
- Assume some familiarity with the service category — a reader who has used a plumber before does not need plumbing explained from first principles
- Explain specialised terms briefly where they add precision or are unavoidable
- Emphasis can shift toward service quality, process, and decision-making guidance

**Advanced**
- More detailed technical terminology may be appropriate when content targets trade professionals, facility managers, or informed buyers
- Assume familiarity with service processes, equipment types, and industry conventions

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Trust and Reliability Signals

Local service customers frequently prioritise trustworthiness and reliability over price when selecting a provider. Credibility in this sector is built through clear, honest service description rather than promotional assertion. Generation skills may reference the following guidance:

- Services should be described in clear, practical terms that give the reader a realistic picture of what to expect
- Reliability and professionalism may be emphasised where the brief supports it — through process description, credentials, or service standards rather than bare claims
- Safety and responsible service practices may be highlighted where relevant to the service type
- Avoid exaggerated claims: "guaranteed perfect results", "instant fixes for every situation", "we always solve it first time" are not credible and may set expectations the service cannot meet

The standard to hold is: would a local customer searching for a trustworthy service provider find this reassuring and believable, or would they find it promotional, vague, or too good to be true?

---

## Service Description Expectations

Local services content frequently needs to explain what a service involves and what the customer can expect from the process. Generation skills may reference the following pattern when constructing service-oriented content in this sector:

1. **The customer problem** — describe the typical problem or situation that brings a customer to this service
2. **The service provided** — explain what the service does to address that problem, in practical terms
3. **What to expect** — outline what happens during the service appointment, visit, or process

**Avoid:**
- Descriptions that focus only on promotional language without explaining what the service actually involves
- Implying service outcomes or capabilities not confirmed in the brief or context material
- Vague service claims ("we handle everything", "complete solutions") without explaining what is included
- Omitting process context that would help a customer understand what they are booking

When `CONTENT_BRIEF.content_points` references specific services, service scenarios, or customer problems, generation skills can follow this explanatory pattern for each point.

---

## Tone Calibration

The default tone for Local Services content prioritises clarity, reliability, and reassurance. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Straightforward, plain-language explanations that are easy to act on
- Practical service descriptions that help the reader understand what they are getting
- Language that builds trust with local customers — honest, direct, and free of pressure
- Warmth and approachability where the service type and audience warrant it (personal care, health services, family-oriented businesses)

**Avoid:**
- Exaggerated marketing hype that is inconsistent with the direct, personal nature of local service relationships
- Aggressive promotional language or high-pressure urgency tactics
- Unrealistic service guarantees that could not be delivered in practice
- Overly corporate or impersonal tone that distances the content from the local, relationship-based nature of the sector

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, a more professional tone for a medical or legal local service), generation skills can follow the brief's tone while maintaining trustworthy and honest framing within that register.

---

## Disclaimer Awareness

Some local services content requires disclaimers, particularly when content touches on safety, service guarantees, pricing, or licensing. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- Discusses safety considerations relevant to the service (electrical, gas, structural, medical, dental)
- References service guarantees, warranties, or workmanship commitments
- Includes or implies pricing estimates, call-out fees, or service costs
- Addresses licensing, certification, or regulatory requirements for the service type

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Local services content carries hallucination risk in several specific areas. Generation skills may reference this guidance when applying source discipline in this sector:

- **Service locations and coverage areas** — do not invent cities, suburbs, postcodes, or geographic service areas not confirmed in the brief or context material
- **Licensing and certification claims** — do not fabricate trade licences, professional certifications, regulatory approvals, or insurance claims
- **Pricing and estimates** — do not invent call-out fees, service rates, or job cost estimates
- **Service guarantees** — do not assert workmanship guarantees, satisfaction guarantees, or warranty terms not confirmed in the source material
- **Emergency and availability claims** — do not imply 24/7 availability, same-day service, or emergency response capability unless confirmed in the brief
- **Safety outcomes** — do not assert that a service eliminates risk or guarantees safety outcomes

All factual claims about service coverage, credentials, pricing, and availability should come from the user's brief or provided context material.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe the service category and typical process in general terms rather than asserting specific claims about the provider that have not been confirmed.
