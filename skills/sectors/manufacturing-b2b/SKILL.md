# Manufacturing / B2B Sector Overlay

**Sector:** Manufacturing / B2B
**CONTENT_BRIEF.sector value:** `manufacturing-b2b`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = manufacturing-b2b`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility expectations, tone, technical specificity, and procurement and decision-maker framing for Manufacturing / B2B content.

---

## Sector Scope

This overlay applies when the content subject falls within the Manufacturing / B2B sector. Typical signals include:

- Manufacturing companies and industrial producers
- Industrial equipment and machinery providers
- B2B product suppliers and component manufacturers
- Engineering services and technical consultancies
- Industrial technology and automation solutions
- Production systems and process optimisation services
- Supply chain, procurement, and logistics operations
- Factory operations and maintenance services

Common terminology signals: manufacturing, industrial, B2B, procurement, production, engineering, supply chain, industrial equipment, factory operations, process optimisation, technical specifications.

**YMYL classification:** Partial. Manufacturing and B2B content involving safety-critical systems, regulatory compliance, or significant procurement commitments carries elevated responsibility expectations. Standard generation rules apply for general informational and process-oriented content.

---

## Technical Content Expectations

Manufacturing and industrial content frequently involves technical descriptions of products, systems, processes, and operational environments. Technical accuracy is a baseline expectation for this sector — imprecision undermines credibility with professional audiences.

Generation skills may reference the following expectations when approaching technical content in this sector:

- Technical descriptions of products and systems should be precise and factual — explain what something does and how it operates rather than relying on marketing language
- Product and system capabilities should be explained clearly in functional and operational terms
- Processes should be described in practical, operational language that reflects real industrial environments
- The gap between specification and real-world operational performance should not be obscured — technical buyers understand this distinction

Avoid descriptions that substitute marketing language for technical explanation, or that assert capability without explaining the underlying function or mechanism.

---

## Terminology Guidance

Industrial and B2B content uses specialised technical, engineering, and procurement terminology that should be applied accurately. Imprecise use of technical or industry terms erodes credibility with professional audiences.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Explain industrial and engineering terminology clearly on first use
- Avoid unexplained technical jargon — OEM, MRO, SCADA, PLC, SLA, throughput, yield, tolerance, and similar terms should be defined before use
- Emphasise practical understanding of what a system, process, or product does and why it matters
- Assume the reader may be unfamiliar with manufacturing process conventions, equipment classifications, or supply chain terminology

**Intermediate**
- Assume basic familiarity with common industrial processes and B2B operational concepts
- Reduce introductory explanations for widely understood terminology
- Emphasise operational benefits, system capabilities, and practical implementation considerations
- Use standard engineering and manufacturing vocabulary accurately without over-explaining

**Advanced**
- Precise engineering, manufacturing, and supply chain terminology may be used without simplification
- Assume familiarity with industrial process design, equipment performance parameters, procurement frameworks, and regulatory standards
- Prioritise technical depth, specificity, and operational accuracy over accessibility scaffolding

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Procurement and Decision-Maker Framing

Manufacturing and B2B content typically targets professional decision-makers rather than general consumers. Purchasing decisions in this sector often involve multiple stakeholders — engineers, procurement managers, operations leaders, and financial approvers — each with different evaluation criteria.

Generation skills may reference the following expectations when framing content for this sector:

- Value should be explained in operational and business terms: reliability, performance under load, scalability, lifecycle cost, integration with existing systems, and maintenance requirements are decision-relevant criteria
- Content should address the decision-maker's operational context — what problem does this solve, what does it replace or improve, and what does implementation involve
- Technical buyers evaluate claims critically — vague assertions of superiority or benefit without supporting operational detail are less credible than specific, grounded explanations
- Avoid framing that treats industrial buyers as casual consumers — purchasing cycles, evaluation processes, and risk tolerance differ substantially in B2B and industrial contexts

---

## Claim Discipline

Industrial content sometimes includes claims about performance, efficiency, cost savings, or operational improvement. Generation skills may reference the following guidance when handling performance and outcome claims in this sector:

- Performance and efficiency claims should be described realistically and with appropriate operational context
- Avoid claims such as "guaranteed efficiency improvements", "universal performance gains", or "zero-failure operation" — these are not credible to technical buyers who understand system variability and operational conditions
- Operational improvements and productivity claims should acknowledge that outcomes depend on implementation quality, existing infrastructure, operational environment, and process design
- Service value should be communicated through technical capability and process quality rather than implied certainty about outcomes

When `CONTENT_BRIEF.content_points` includes performance claims or operational benefit statements, generation skills can reference this discipline when working with those points.

---

## Credibility Expectations

Manufacturing and B2B audiences expect technically credible, operationally grounded content. Credibility in this sector is built through accurate technical description, realistic operational framing, and factual product or system representation. Generation skills may reference the following guidance:

- Product capabilities should be described accurately — do not imply specifications, tolerances, or performance characteristics not confirmed in the brief or context material
- Industrial processes and operational environments described in content should reflect real manufacturing conditions rather than idealised scenarios
- Technical benefits should be explained in practical terms — what the benefit is, what produces it, and under what conditions it applies
- Avoid fabricating product specifications, inventing performance metrics, or implying capabilities that the brief does not confirm

The standard to hold is: would an experienced engineer, procurement manager, or operations leader find this technically credible and operationally useful, or would they find it vague, overstated, or inconsistent with their knowledge of how industrial systems actually work?

---

## Tone Calibration

The default tone for Manufacturing / B2B content emphasises technical clarity, professional credibility, and practical operational value. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Clear, precise explanations of systems, products, and processes in operational terms
- Professional, technically competent voice that reflects domain knowledge
- Realistic discussion of operational challenges and how solutions address them
- Confidence grounded in specific technical detail rather than promotional assertion

**Avoid:**
- Exaggerated marketing hype that substitutes enthusiasm for technical substance ("revolutionary", "game-changing", "industry-disrupting")
- Overly casual language that reads as inappropriate for professional industrial or procurement audiences
- Unrealistic performance promises that contradict how industrial systems and processes actually behave
- Generic B2B language that could describe any product in any industry without modification

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, more accessible language for a non-technical stakeholder audience), generation skills can follow the brief's tone while maintaining technical accuracy and credibility within that register.

---

## Process and System Explanation

Manufacturing and B2B content frequently requires explaining industrial systems, production workflows, or operational processes. Generation skills may reference the following pattern when constructing process or system content in this sector:

1. **The operational challenge** — describe the production problem, process gap, or operational requirement the system or solution addresses
2. **The system or solution** — explain what it is, how it functions, and what it does in operational terms
3. **The implementation or operational impact** — outline how it integrates into existing operations and what practical improvement it delivers

**Avoid:**
- Presenting technical outcomes without explaining the underlying system, mechanism, or process that produces them
- Using vague industrial buzzwords without explaining what they mean in operational terms ("smart manufacturing", "Industry 4.0", "digital transformation" as standalone claims without grounding)
- Implying operational performance results not confirmed in the brief or context material
- Describing systems or processes in isolation without acknowledging integration requirements or operational dependencies

When `CONTENT_BRIEF.content_points` references specific systems, processes, or technical solutions, generation skills can follow this explanatory pattern for each point.

---

## Disclaimer Awareness

Some manufacturing and industrial content requires disclaimers, particularly when content touches on safety, regulatory compliance, or product performance commitments. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- Discusses safety-critical systems, equipment, or operational procedures
- References regulatory compliance standards, certification requirements, or industrial safety obligations
- Addresses product performance guarantees, service-level commitments, or warranty terms
- Involves significant procurement commitments or capital expenditure decisions

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Manufacturing and B2B content carries hallucination risk in several specific areas. Generation skills may reference this guidance when applying source discipline in this sector:

- **Technical specifications** — do not invent product dimensions, material grades, operating tolerances, load ratings, or performance parameters not confirmed in the brief or context material
- **Performance metrics** — do not fabricate efficiency figures, throughput rates, cycle times, or productivity improvement statistics
- **Compliance certifications** — do not assert that a product, system, or facility holds specific regulatory certifications, standards compliance, or safety approvals unless confirmed in the source material
- **Regulatory requirements** — do not invent jurisdiction-specific industrial regulations, environmental standards, or safety obligations
- **Operational statistics** — do not fabricate failure rates, uptime figures, maintenance intervals, or lifecycle cost data
- **Industrial standards references** — do not assert conformance with named standards (ISO, IEC, ASTM, CE, etc.) unless confirmed in the source material

All factual claims about technical specifications, performance, compliance, and regulatory requirements should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe general manufacturing practice, system category behaviour, or industrial process principles rather than asserting specific technical facts or compliance claims that have not been confirmed.
