# HR / Recruitment Sector Overlay

**Sector:** HR / Recruitment
**CONTENT_BRIEF.sector value:** `hr-recruitment`

This file provides behavioural guidance for generation skills when `CONTENT_BRIEF.sector = hr-recruitment`. It is not a generation skill. It does not create content, define structure, or control templates. Generation skills reference this file to calibrate terminology, credibility expectations, tone, role-specific framing, and hiring claim discipline for HR / Recruitment content.

---

## Sector Scope

This overlay applies when the content subject falls within the HR / Recruitment sector. Typical signals include:

- Recruitment agencies and staffing firms
- In-house HR departments and talent acquisition functions
- Hiring processes and candidate evaluation frameworks
- Talent acquisition strategy and workforce planning
- Employee onboarding and retention programmes
- Staffing, placement, and contract recruitment services
- Employer branding and candidate experience content

Common terminology signals: recruitment, hiring, talent acquisition, candidate, interview, job role, employer, HR, workforce, staffing, placement, onboarding.

**YMYL classification:** Partial. HR and recruitment content involving employment law, compensation guidance, or significant hiring commitments carries elevated responsibility expectations. Standard generation rules apply for general informational and process-oriented content.

---

## Role-Specific Content

Recruitment content often centres on specific job roles, hiring needs, or candidate profiles. Generic treatment of roles without explaining responsibilities, context, or expectations produces content that is not useful to hiring managers, candidates, or HR professionals.

Generation skills may reference the following expectations when handling role-specific content in this sector:

- Job roles should be clearly defined — titles alone do not communicate scope, seniority, or expectations
- Responsibilities and role requirements should be described accurately and specifically rather than in generic terms
- Hiring processes should be explained realistically — what candidates can expect and what the process involves
- Audience perspective matters — content may serve employers, candidates, or HR professionals, and the framing should reflect the intended reader's needs

Avoid descriptions that treat job roles as interchangeable or that imply responsibilities without explaining them.

---

## Terminology Guidance

HR and recruitment content uses specialised terminology related to hiring practices, talent management, and workforce strategy. Terminology depth can follow `CONTENT_BRIEF.reader_knowledge_level`. Generation skills may reference the guidance below when calibrating terminology for this sector.

**Beginner**
- Explain HR and recruitment terminology clearly on first use
- Avoid unexplained jargon — ATS, JD, SLA, RPO, EVP, headcount, and similar shorthand should be defined before use
- Emphasise practical hiring understanding — what processes mean for the employer or candidate in concrete terms
- Assume the reader may be unfamiliar with formal HR frameworks, hiring stage conventions, or workforce planning terminology

**Intermediate**
- Assume basic familiarity with common hiring processes and HR concepts
- Reduce introductory explanations for widely understood terminology
- Focus on recruitment strategy, decision-making frameworks, and process quality
- Use standard HR and talent acquisition vocabulary accurately without over-explaining

**Advanced**
- Precise HR, talent acquisition, and workforce planning terminology may be used without simplification
- Assume familiarity with organisational design concepts, structured interviewing frameworks, compensation benchmarking, and labour market analysis
- Prioritise depth and strategic specificity over accessibility scaffolding

Generation skills rely on `CONTENT_BRIEF.reader_knowledge_level` populated during intake. Sector overlays must not infer or modify this value.

---

## Hiring Claim Discipline

Recruitment content sometimes includes claims about hiring outcomes, placement speed, or candidate quality. These are an area of elevated claim risk — both employers and candidates are sensitive to overpromising. Generation skills may reference the following guidance when handling hiring outcome claims:

- Hiring outcomes should not be presented as guaranteed — success depends on role requirements, market conditions, candidate availability, and the hiring organisation's process
- Recruitment timelines vary depending on industry, seniority, and role complexity — avoid implying fixed or typical timeframes without qualification
- Avoid claims such as "guaranteed placements", "instant hiring results", or "perfect candidate matches"
- Service value should be communicated through process quality, methodology, and expertise rather than implied certainty about outcomes

When `CONTENT_BRIEF.content_points` includes hiring outcome claims or service promises, generation skills can reference this discipline when working with those points.

---

## Credibility Expectations

HR and recruitment audiences — whether employers, candidates, or HR professionals — expect realistic, process-grounded representation of hiring practices and recruitment services. Credibility in this sector is built through accurate role framing, honest process description, and responsible handling of outcomes and benchmarks. Generation skills may reference the following guidance:

- Recruitment processes and service descriptions should reflect real, recognisable hiring practices
- Job role and candidate profile descriptions should be accurate — vague or inflated descriptions set incorrect expectations for both employers and candidates
- Hiring advice should be practical and grounded in how recruitment actually works rather than idealised or theoretical
- Avoid fabricating hiring statistics, candidate success rates, or placement figures not confirmed in the brief or context material

The standard to hold is: would an experienced HR professional or hiring manager find this credible and practically useful, or would they find it generic, overstated, or disconnected from real hiring practice?

---

## Tone Calibration

The default tone for HR / Recruitment content balances professionalism, clarity, and supportive guidance. Generation skills can follow this guidance when calibrating tone:

**Encourage:**
- Respectful, balanced language about both candidates and employers
- Practical explanations of hiring processes that are useful to the intended reader
- Supportive framing for candidates navigating hiring processes or career decisions
- Professional confidence in discussing recruitment strategy and talent acquisition methodology

**Avoid:**
- Exaggerated hiring promises that set unrealistic expectations for employers or candidates
- Dismissive or reductive language about candidates, roles, or hiring challenges
- Unrealistic employment expectations — not all roles are filled quickly, not all candidates are immediately successful
- Jargon-heavy framing that excludes the candidate or general reader when the content is intended for a broad audience

`CONTENT_BRIEF.tone` takes precedence. If the brief specifies a different tone (for example, direct and assertive for an employer-facing recruitment services page), generation skills can follow the brief's tone while maintaining professional and respectful framing within that register.

---

## Hiring Process Explanation

Recruitment and HR content frequently requires explaining hiring processes, talent acquisition strategies, or evaluation frameworks. Generation skills may reference the following pattern when constructing process-oriented content in this sector:

1. **The hiring challenge** — describe the role requirement, talent gap, or recruitment objective
2. **The recruitment approach** — explain the sourcing strategy, channels used, and candidate engagement method
3. **The evaluation or selection process** — outline how candidates are assessed, shortlisted, and progressed

**Avoid:**
- Presenting hiring outcomes without explaining the process that produced them
- Implying specific results (placement rates, time-to-hire, candidate quality scores) that are not confirmed in the brief or context material
- Using vague HR buzzwords without explaining what they mean in practice ("culture fit", "talent pipeline", "best-in-class hiring" as standalone claims)
- Presenting one-size-fits-all hiring advice without acknowledging that process design depends on role, industry, and organisational context

When `CONTENT_BRIEF.content_points` references specific hiring processes, recruitment strategies, or case studies, generation skills can follow this pattern for each point.

---

## Disclaimer Awareness

Some HR and recruitment content requires disclaimers, particularly when content touches on employment law, compensation guidance, or service commitments. Generation skills may reference this guidance to recognise when disclaimer placement is likely required.

Disclaimers may be expected when content:

- Discusses employment law considerations, rights, or obligations
- Addresses compensation, salary benchmarks, or benefits guidance
- References recruitment guarantees, replacement terms, or service-level commitments
- Involves hiring practices that vary by jurisdiction or regulatory environment

The overlay does not insert disclaimers. Disclaimer requirements are controlled by `CONTENT_BRIEF.disclaimers_required`. Generation skills handle placement and wording. If `CONTENT_BRIEF.disclaimers_required` is set, generation skills apply disclaimer logic according to their own rules.

---

## Hallucination Safety

HR and recruitment content carries hallucination risk in several specific areas. Generation skills may reference this guidance when applying source discipline in this sector:

- **Hiring statistics** — do not invent time-to-hire figures, offer acceptance rates, or placement volumes not confirmed in the brief or context material
- **Labour market data** — do not fabricate employment trend statistics, talent shortage figures, or sector-specific demand data
- **Salary benchmarks** — do not invent compensation ranges, pay benchmarks, or salary survey data unless confirmed in the source material
- **Candidate success rates** — do not fabricate placement success rates, candidate retention figures, or interview pass rates
- **Employment law** — do not assert jurisdiction-specific legal obligations, compliance requirements, or statutory entitlements unless confirmed in the source material
- **Platform or tool capabilities** — do not invent features or performance characteristics of named ATS platforms, job boards, or HR technology tools

All factual claims about hiring outcomes, labour market conditions, salary benchmarks, and employment law should come from the user's brief, provided context material, or verified sources retrieved during generation.

If `CONTENT_BRIEF.hallucination_risk_components` is populated, generation skills can treat those components with maximum caution and avoid asserting specifics beyond what the brief confirms.

When in doubt, describe general recruitment practice or hiring category behaviour rather than asserting specific metrics or legal facts that have not been confirmed.
