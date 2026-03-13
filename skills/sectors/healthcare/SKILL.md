# Healthcare Sector Overlay

**Sector:** Healthcare
**CONTENT_BRIEF.sector value:** `healthcare`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = healthcare`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility standards, claim discipline, and tone for Healthcare content.

---

## Sector Scope

This overlay applies when the content subject falls within the Healthcare sector. Typical signals include:

- Hospitals, clinics, and healthcare facilities
- Doctors, specialists, and healthcare professionals
- Pharmaceuticals and drug treatments
- Medical research and clinical findings
- Therapy and mental health services
- Patient care, wellness, and prevention
- Medical procedures and diagnostics
- Healthcare policy and public health

Common terminology signals: hospital, clinic, doctor, medical, healthcare, therapy, mental health, pharma, treatment, patient, diagnosis, medical research.

---

## YMYL Classification

**YMYL classification:** YMYL. Healthcare content can directly influence health decisions, medical treatment understanding, and patient safety. Generation skills may reference this classification when applying heightened claim discipline and credibility expectations.

Content discussing symptoms, treatments, diagnoses, or medical procedures warrants extra caution. Generation skills can treat these topic areas with stricter source discipline and more careful framing than would apply in non-YMYL sectors.

The overlay does not insert medical disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required` and `CONTENT_BRIEF.sector_flags`. Generation skills handle disclaimer placement and wording.

---

## Terminology Guidance

Healthcare content requires precise medical terminology. Casual simplification that alters the meaning of a medical concept can introduce inaccuracy and undermine credibility.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating medical terminology for this sector.

**Beginner**
- Define medical terms clearly on first use
- Explain conditions, procedures, and treatments in plain language before using clinical shorthand
- Avoid assuming familiarity with anatomical terminology, diagnostic classifications, or drug names
- Use explanatory framing: "X (which refers to Y)" rather than unexplained clinical terms

**Intermediate**
- Assume basic familiarity with common health and medical concepts
- Reduce or omit definitions for widely understood terminology
- Retain precision — do not oversimplify to the point of clinical inaccuracy

**Advanced**
- Precise medical and clinical terminology may be used without oversimplification
- Assume familiarity with anatomical, pharmacological, and procedural conventions
- Avoid explaining established clinical concepts that a professional or informed audience would consider foundational

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Medical Information Safety

Healthcare content must avoid presenting information in ways that could be interpreted as personalised medical advice.

Generation skills may reference the following guidance when framing health and medical content:

**Favour:**
- Educational explanations of conditions, treatments, and procedures
- General health information and condition awareness
- Prevention, wellness, and public health framing
- Clear statements that individual medical decisions depend on consultation with a qualified healthcare professional

**Avoid:**
- Personalised diagnosis framing — content should not imply it can identify what a specific reader has
- Personalised treatment recommendations — content should not prescribe what a specific reader should do
- Guaranteed medical outcomes — health results depend on individual circumstances and professional oversight

This guidance applies across all formats. A blog post, a webpage, and a social post in this sector all benefit from maintaining a clear distinction between general health education and personal medical advice.

---

## Credibility Expectations

Healthcare audiences expect strong credibility signals. Authority in this sector is built through factual accuracy, responsible framing, and clear differentiation between established knowledge and emerging or contested findings. Generation skills may reference the following guidance:

- Ground medical explanations in established clinical understanding
- Clearly differentiate between symptoms, conditions, diagnoses, and treatments — these are not interchangeable
- Explain risks, limitations, or contraindications where relevant rather than presenting treatments as uniformly safe or effective
- Avoid exaggerated health claims — "miracle cure", "guaranteed recovery", or "eliminates all risk" framing damages credibility with informed readers
- Avoid presenting anecdotal outcomes as representative medical results

The standard to hold is: would a medically informed reader find this accurate and responsibly framed, or would they find it overstated, misleading, or unsafe?

---

## Tone Calibration

The default tone for Healthcare content is professional, careful, responsible, and — where relevant to patient-facing material — empathetic. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Clear, measured language that reflects clinical responsibility
- Empathetic framing when addressing patient experiences, mental health, or difficult diagnoses
- Factual confidence grounded in established medical understanding
- Appropriate acknowledgement of complexity or uncertainty where it exists

**Avoid:**
- Sensational health claims ("this one thing cures X")
- Fear-based messaging that amplifies anxiety without educational purpose
- Exaggerated promises about treatment outcomes or recovery timelines
- Minimising language that dismisses the seriousness of conditions or patient concerns

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, conversational for public education material), generation skills can follow the brief's tone while maintaining responsible medical framing within that register.

---

## Claim Discipline

Medical topics require strict claim discipline. Generation skills may reference the following guidance when handling claims in this sector:

- Avoid guaranteeing treatment outcomes — medical results depend on individual patient factors, clinical context, and professional assessment
- Avoid presenting early-stage or preliminary research findings as established medical consensus
- Avoid implying medical certainty where clinical variability or ongoing debate exists
- Explain limitations or conditions affecting medical evidence where relevant
- Prefer language that reflects appropriate uncertainty: "evidence suggests", "may help", "in some cases", "depending on individual circumstances"
- Avoid attributing medical claims to unnamed or unverified experts

Educational framing — explaining what is known about a condition or treatment — is preferable to advisory instruction that directs the reader toward specific health decisions.

---

## Disclaimer Guidance

Healthcare content often requires disclaimers. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- Discusses symptoms or conditions that readers may self-identify with
- Explains treatment methods or drug interactions
- Describes medical procedures or diagnostic approaches
- Interprets research findings that have clinical implications

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Healthcare content carries high hallucination risk. Generation skills may reference this guidance when applying source discipline in this sector:

- **Medical research findings** — do not invent study results, trial outcomes, or research conclusions not confirmed in the brief or context material
- **Treatment effectiveness** — do not fabricate efficacy claims, success rates, or comparative treatment outcomes
- **Clinical statistics** — do not invent prevalence figures, mortality rates, or epidemiological data
- **Regulatory approvals** — do not assert that a treatment, drug, or device has regulatory approval unless confirmed in the source material
- **Expert attribution** — do not attribute medical claims to unnamed experts or fabricate citations to medical bodies or journals
- **Drug or dosage information** — do not invent dosage guidelines, contraindications, or drug interaction information

All factual medical claims should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe established general understanding of a condition, treatment category, or health concept rather than asserting specific clinical facts that have not been confirmed.
