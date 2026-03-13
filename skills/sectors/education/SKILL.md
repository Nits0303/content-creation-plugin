# Education Sector Overlay

**Sector:** Education
**CONTENT_BRIEF.sector value:** `education`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = education`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility expectations, tone, and explanatory approach for Education content.

---

## Sector Scope

This overlay applies when the content subject falls within the Education sector. Typical signals include:

- Schools, colleges, and universities
- Online education platforms and course providers
- Edtech tools and learning management systems
- Professional training and development programmes
- Certification and credentialling courses
- Curriculum design and instructional methodology
- Teaching methods and pedagogical approaches
- Student learning resources and study guides

Common terminology signals: school, college, university, course, curriculum, learning, education, training, certification, student, teacher, edtech.

**YMYL classification:** Partial. Education content involving certification requirements, career outcome claims, licensing obligations, or significant financial commitments (such as tuition) carries elevated responsibility expectations. Standard generation rules apply for general informational and explanatory content.

---

## Audience Learning Context

Education content frequently serves readers who are actively trying to learn or understand something new. The primary job of content in this sector is often to build understanding, not simply to describe.

Generation skills may reference the following expectations when calibrating explanatory approach:

- Prioritise clarity and comprehension over completeness — a reader who understands a concept well benefits more than a reader who has been exposed to every detail
- Complex ideas benefit from structured, step-by-step explanation rather than dense presentation
- Examples support learning comprehension and reduce the cognitive load of abstract concepts
- Content should aim to help the reader understand, not merely inform them that something exists

This guidance applies across all formats. A blog post, a webpage, and a social post in this sector all benefit from maintaining a learner-oriented explanatory orientation at the appropriate depth for the format.

---

## Terminology Guidance

Educational content often involves specialised or domain-specific terminology that may not be familiar to all readers. Unexplained jargon is a particular credibility and accessibility risk in this sector.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Define terms clearly on first use
- Avoid unexplained jargon or acronyms — spell out and briefly explain before using shorthand
- Use examples or analogies to clarify unfamiliar concepts
- Assume no prior domain knowledge unless the brief indicates otherwise

**Intermediate**
- Assume basic familiarity with the topic area
- Reduce or omit introductory definitions for commonly understood terms
- Focus on expanding understanding rather than re-establishing foundational concepts
- Examples remain useful for nuanced or applied concepts

**Advanced**
- Technical or academic terminology may be used without oversimplification
- Assume familiarity with domain vocabulary and established frameworks
- Prioritise depth, nuance, and precision over accessibility scaffolding
- Avoid over-explaining concepts that an informed reader would consider foundational

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Credibility Expectations

Educational content benefits from accurate, responsible representation of learning outcomes, pathways, and programmes. Credibility in this sector is built through honest framing and clear differentiation between what education can reliably deliver and what depends on individual circumstances. Generation skills may reference the following guidance:

- Explain concepts accurately — misrepresentation of educational or academic content damages trust and may mislead learners
- Represent educational outcomes responsibly — learning results depend on individual effort, prior knowledge, and context
- Distinguish clearly between established knowledge and opinion, emerging practice, or contested pedagogy
- Avoid exaggerated claims about learning outcomes: "guaranteed job placement", "instant mastery", "learn everything in days" are not credible and undermine reader trust
- Present educational pathways and programme structures realistically — avoid implying that a qualification or course is simpler, faster, or more certain in its outcomes than is accurate

The standard to hold is: would an informed educator or experienced learner find this accurate and responsibly framed, or would they find it overstated or misleading?

---

## Tone Calibration

The default tone for Education content is clear, supportive, informative, and encouraging of learning. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Patient, learner-friendly language that meets the reader where they are
- Explanations that prioritise understanding over demonstration of complexity
- Encouraging framing that supports learning confidence without making exaggerated promises
- Examples and illustrations that make abstract concepts accessible

**Avoid:**
- Overly academic or dense language when the audience is general or beginner-level
- Exaggerated motivational claims about learning success ("change your life overnight", "become an expert in weeks")
- Condescending simplification for intermediate or advanced audiences
- Dry, clinical tone that disengages learners in content intended to encourage participation

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, formal academic writing for a university context), generation skills can follow the brief's tone while maintaining clarity and appropriate learner orientation within that register.

---

## Learning Explanation Guidance

Educational content frequently requires explaining concepts, skills, or processes. Generation skills may reference the following pattern when constructing explanations in this sector:

1. **What it is** — define or describe the concept clearly
2. **Why it matters** — explain the significance, relevance, or purpose of understanding it
3. **How it applies** — illustrate with a real-world example, use case, or practical scenario

This pattern supports comprehension across all formats and knowledge levels. Depth and length of each element should reflect the format and `CONTENT_BRIEF.reader_knowledge_level`.

**Avoid explanations that:**
- Assume knowledge the reader may not yet have
- Rely on unexplained terminology that blocks understanding
- Present concepts in isolation without context or application
- Skip from definition directly to advanced application without bridging explanation

When `CONTENT_BRIEF.content_points` references specific concepts, skills, or topics, generation skills can follow this explanatory pattern for each point.

---

## Disclaimer Awareness

Some education content requires disclaimers, particularly when content touches on certification requirements, career outcomes, academic pathways, or financial commitments. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- Discusses professional certifications, licensing requirements, or regulatory qualifications
- Describes career outcomes or employment prospects associated with a course or programme
- Explains academic programme structures, entry requirements, or institutional policies
- Involves significant financial commitments such as tuition, enrolment, or course fees

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Education content carries hallucination risk in several specific areas. Generation skills may reference this guidance when applying source discipline in this sector:

- **Certification requirements** — do not invent eligibility criteria, examination structures, passing scores, or renewal requirements for professional or academic certifications
- **Institutional policies** — do not fabricate admission requirements, programme structures, credit transfer rules, or graduation criteria for named institutions
- **Job outcome statistics** — do not invent placement rates, salary outcomes, or employment statistics associated with specific programmes or qualifications
- **Academic requirements** — do not assert specific coursework, credit hours, or prerequisite structures that have not been confirmed in the brief or context material
- **Accreditation status** — do not assert that a programme, institution, or qualification holds accreditation unless confirmed in the source material
- **Regulatory licensing rules** — do not invent jurisdiction-specific licensing requirements associated with educational credentials

All factual claims about certification, accreditation, academic structure, and career outcomes should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe how a category of qualification or educational pathway generally works rather than asserting specific institutional or regulatory facts that have not been confirmed.
