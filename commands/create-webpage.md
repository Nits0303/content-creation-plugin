# /create-page

---

## Command Purpose

`/create-page` is the entry point for webpage content generation. It signals that the user wants to create a web page and sets `CONTENT_BRIEF.format = webpage`. The command triggers the pipeline. It contains no generation logic.

---

## When This Command Is Used

Use `/create-page` when the user wants to generate any web page. This includes all 16 webpage subtypes across all 6 structural families:

- Homepage
- About Page
- Services Page
- Product Page
- Landing Page
- Sales Page
- Pricing Page
- FAQ Page
- Case Study Page
- Industry Page
- Portfolio Page
- Testimonials Page
- Privacy Policy / Terms Page
- Contact Page
- Thank You Page
- 404 Error Page

If the user invokes `/create` without specifying a format, the router determines whether the request is a webpage, blog, or social post. `/create-page` is used when the format is explicitly webpage.

---

## Pipeline Activation

Invoking `/create-page` starts the following pipeline:

```
/create-page → intake/SKILL.md → router/SKILL.md → webpage/SKILL.md → webpage-templates/SKILL.md → humanise/SKILL.md
```

`CONTENT_BRIEF.format` is set to `webpage` at command invocation. All subsequent pipeline stages read this value. `router/SKILL.md` reads `CONTENT_BRIEF.format` and activates `webpage/SKILL.md`. Subtype determination happens inside `webpage/SKILL.md`.

---

## What Happens After The Command Runs

1. `intake/SKILL.md` opens. If context material is present (URL, uploaded document, pasted copy), Pre-Phase 1 context extraction fires first and populates the brief silently before any questions are asked.
2. Intake runs its phase sequence to complete `CONTENT_BRIEF`. Sector is inferred silently. `visitor_familiarity` is inferred silently from the Visitor Profile answer.
3. `router/SKILL.md` reads `CONTENT_BRIEF.format` and activates `webpage/SKILL.md`. Subtype determination happens inside `webpage/SKILL.md`. If two or more subtypes remain equally plausible, the Subtype Routing Checkpoint fires inside `webpage/SKILL.md`.
4. `webpage/SKILL.md` generates the page using the confirmed subtype, structural family, and completed `CONTENT_BRIEF`.
5. `webpage-templates/SKILL.md` applies the correct section architecture for the confirmed subtype.
6. `humanise/SKILL.md` runs the humanise pass on the generated output.
7. The finished page is returned to the user.

---

## Output Behaviour

- One webpage is generated per command invocation.
- Output format follows `CONTENT_BRIEF.output_format`.
- Output length follows `CONTENT_BRIEF.length_tier` and the section count rules defined for the confirmed subtype in `webpage-templates/SKILL.md`.
- SEO layer fires for all subtypes except 404 Error Page and Thank You Page.
- If `cta_allowed = false`, no CTA is generated regardless of subtype defaults.
- Sector overlay fires if a sector SKILL.md file exists for the confirmed `CONTENT_BRIEF.sector` value.

---

## Restrictions

- This command file contains no intake logic, routing logic, generation logic, template logic, SEO logic, humanise logic, or sector overlay logic.
- All logic lives in the skill files activated by this pipeline.
- Do not modify this command file to add generation behaviour.
- Do not use this command to generate blog posts or social content. Use `/create-blog` or `/create-social` for those formats.
