# /create-social

---

## Command Purpose

`/create-social` is the entry point for social media content generation. It signals that the user wants to create social content and sets `CONTENT_BRIEF.format = social`. The command triggers the pipeline. It contains no generation logic.

---

## When This Command Is Used

Use `/create-social` when the user wants to generate any social media content. This includes all 15 social subtypes across all 3 structural families:

**Family 1 — Single-Unit Posts**
- Text Post
- Image Caption
- Announcement Post
- Promotional Post
- Engagement Post
- Event Post
- Testimonial Post
- Hashtag Campaign Post

**Family 2 — Multi-Unit Formats**
- Carousel
- Story
- Poll
- Thread

**Family 3 — Long-Form & Scripted Content**
- Short Video Script
- Long Video Script
- LinkedIn Article

If the user invokes `/create` without specifying a format, the router determines whether the request is a blog, webpage, or social post. `/create-social` is used when the format is explicitly social.

---

## Pipeline Activation

Invoking `/create-social` starts the following pipeline:

```
/create-social → intake/SKILL.md → router/SKILL.md → social/SKILL.md → social-templates/SKILL.md → humanise/SKILL.md
```

`CONTENT_BRIEF.format` is set to `social` at command invocation. All subsequent pipeline stages read this value. `router/SKILL.md` reads `CONTENT_BRIEF.format` and activates `social/SKILL.md`. Subtype determination happens inside `social/SKILL.md`.

---

## What Happens After The Command Runs

1. `intake/SKILL.md` opens. If context material is present (URL, uploaded document, pasted copy), Pre-Phase 1 context extraction fires first and populates the brief silently before any questions are asked.
2. Intake runs its phase sequence to complete `CONTENT_BRIEF`. Sector is inferred silently. Platform is confirmed during intake and populates `CONTENT_BRIEF.platform`.
3. `router/SKILL.md` reads `CONTENT_BRIEF.format` and activates `social/SKILL.md`. Subtype determination happens inside `social/SKILL.md`. If two or more subtypes remain equally plausible, the Subtype Routing Checkpoint fires inside `social/SKILL.md`.
4. `social/SKILL.md` determines the structural family and subtype, then generates the content using the completed `CONTENT_BRIEF`.
5. `social-templates/SKILL.md` applies the correct structural template for the confirmed subtype.
6. `humanise/SKILL.md` runs the humanise pass on the generated output.
7. The finished social content is returned to the user.

---

## Output Behaviour

- Output quantity and structure follow the confirmed subtype. Single-Unit Posts produce one content block. Multi-Unit Formats produce sequential discrete units. Long-Form & Scripted content produces a structured long-form document.
- Output format follows `CONTENT_BRIEF.output_format`.
- Unit count for Multi-Unit Formats follows `CONTENT_BRIEF.unit_count`, confirmed or recommended during intake.
- Duration-to-structure translation applies for video script subtypes, derived from `CONTENT_BRIEF.duration_target`.
- Platform rules, hashtag logic, and copy length adaptation are applied inside `social/SKILL.md`, not in this command.
- If `cta_allowed = false`, no CTA is generated regardless of subtype defaults.
- Sector overlay fires if a sector SKILL.md file exists for the confirmed `CONTENT_BRIEF.sector` value.

---

## Restrictions

- This command file contains no intake logic, routing logic, generation logic, template logic, SEO logic, humanise logic, or sector overlay logic.
- All logic lives in the skill files activated by this pipeline.
- Do not modify this command file to add generation behaviour.
- Do not use this command to generate blog posts or web pages. Use `/create-blog` or `/create-page` for those formats.
