# Legal / CA / Finance Sector Overlay

**Sector:** Legal / CA / Finance
**CONTENT_BRIEF.sector value:** `legal-ca-finance`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = legal-ca-finance`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, compliance framing, credibility, tone, and claim discipline for Legal / CA / Finance content.

---

## Sector Scope

This overlay applies when the content subject falls within the Legal / CA / Finance sector. Typical signals include:

- Law and legal services
- Taxation and chartered accountancy
- Financial planning and wealth management
- Compliance and regulatory matters
- Investment and markets
- Banking and financial advisory
- Corporate governance and audit

Common terminology signals: law, legal, CA, chartered accountant, tax, taxation, audit, SEBI, RBI, compliance, regulation, financial planning, wealth management.

---

## YMYL Classification

**YMYL classification:** YMYL. This sector directly affects legal standing, financial decisions, and regulatory compliance. Generation skills may reference this classification when applying heightened credibility and claim discipline standards.

Content discussing legal advice, financial advice, or tax advice warrants extra caution. Generation skills can treat these topic areas with stricter source discipline and more careful framing than would apply in non-YMYL sectors.

The overlay does not implement disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required` and `CONTENT_BRIEF.sector_flags`. Generation skills handle disclaimer placement.

---

## Terminology Guidance

Legal and financial content requires precise terminology. Casual paraphrasing of legal or regulatory concepts can introduce inaccuracy and undermine credibility.

Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Define legal and financial terms clearly on first use
- Explain regulatory concepts in plain language before using shorthand
- Avoid assuming familiarity with statutory references, regulatory body names, or legal classifications
- Use explanatory framing: "X (which refers to Y)" rather than unexplained jargon

**Intermediate**
- Assume basic familiarity with common legal and financial concepts
- Reduce or omit definitions for standard terminology
- Retain precision — do not oversimplify to the point of inaccuracy

**Advanced**
- Precise terminology may be used without oversimplification
- Assume familiarity with regulatory frameworks, statutory instruments, and professional conventions
- Avoid explaining established concepts that the audience would consider foundational

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Compliance Language Guidance

Legal and financial content frequently intersects with regulation. Generation skills may reference the following guidance when framing claims and explanations in this sector.

**Favour:**
- Informational and educational framing
- Compliance-aware wording that explains how rules or obligations work
- Language that describes regulatory context without asserting personal legal or financial outcomes

**Avoid:**
- Framing content as guaranteed legal outcomes
- Framing content as guaranteed financial results
- Presenting content as direct professional advice unless the brief explicitly authorises this framing

If `CONTENT_BRIEF.content_goal` or `CONTENT_BRIEF.special_instructions` indicate that the content is intended as professional advisory material authored by a qualified practitioner, generation skills can adjust framing accordingly.

---

## Credibility Expectations

Legal and financial audiences expect strong authority signals. Credibility in this sector is built through accuracy, verified facts, and responsible framing. Generation skills may reference the following guidance:

- Use verifiable facts when discussing laws, regulations, or financial rules
- Avoid vague claims about legal or financial outcomes
- Avoid speculative interpretation of regulatory policy
- Prefer clear references to governing bodies or frameworks where relevant — SEBI, RBI, Income Tax authorities, courts, and statutory bodies may be referenced when directly applicable to the topic
- Reference regulatory bodies accurately and only when the topic warrants it — do not introduce them for credibility effect

The standard to hold is: would a legally or financially informed reader find this accurate and responsibly framed, or would they find it vague, speculative, or misleading?

---

## Tone Calibration

The default tone for Legal / CA / Finance content is professional, precise, measured, and responsible. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Clear, measured language
- Precise factual statements
- Responsible framing of outcomes and obligations
- Calm, authoritative voice appropriate to the subject matter

**Avoid:**
- Sensational financial claims ("guaranteed returns", "never lose money")
- Exaggerated legal outcomes ("always wins", "bulletproof protection")
- Emotionally charged financial promises
- Urgency-driven language that pressures financial or legal decisions

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, conversational for a general audience), generation skills can follow the brief's tone while preserving responsible and compliant framing within that register.

---

## Claim Discipline

Legal and financial content carries elevated risk from imprecise or overconfident claims. Generation skills may reference the following guidance when handling claims in this sector:

- Avoid guaranteed outcomes — legal and financial results depend on individual circumstances
- Avoid definitive legal interpretations presented without authority — prefer explanatory framing over conclusive rulings
- Avoid speculative financial predictions presented as fact
- Use conditional language when discussing outcomes: "this may", "depending on circumstances", "subject to applicable rules"
- Prefer educational framing that explains the factors affecting a legal or financial decision over advisory instruction that tells the reader what to do

---

## Disclaimer Guidance

Legal and financial content often requires disclaimers. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- Discusses legal interpretation or legal obligations
- Describes financial outcomes, investment performance, or returns
- Explains tax rules, compliance requirements, or regulatory obligations

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

Legal and financial content carries high hallucination risk. Generation skills may reference this guidance when applying source discipline in this sector:

- **Laws and regulations** — do not invent legislation, statutory provisions, or regulatory rules not confirmed in the brief or context material
- **Legal precedent** — do not fabricate case law, court rulings, or tribunal decisions
- **Tax rules** — do not invent tax rates, thresholds, exemptions, or filing requirements
- **Financial statistics** — do not invent market figures, return rates, or economic data
- **Regulatory body positions** — do not attribute policies, rulings, or statements to SEBI, RBI, or other bodies unless confirmed in the source material
- **Professional qualifications** — do not assert that a practitioner holds specific qualifications unless stated in the brief

All factual legal and financial claims should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe how a category of law, regulation, or financial principle generally works rather than asserting specific outcomes or rules that have not been confirmed.
