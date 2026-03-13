# commands/create-blog.md

## Command Purpose

`/create-blog` is the user entry point for blog post generation. It activates the content pipeline with format set to `blog` and passes control immediately to the intake skill. No logic, subtype selection, or content generation occurs inside this command.

---

## When This Command Is Used

Use `/create-blog` when the user explicitly wants to generate a blog post of any subtype, including how-to, listicle, opinion, case study, news and trends, product review, interview, guest post, SEO-focused article, beginner's guide, or ultimate guide.

Do not use this command for webpage or social content. Use `/create` if format is unknown or ambiguous at the point of entry.

---

## Pipeline Activation

Running `/create-blog` activates the following pipeline:

```
/create-blog
  → intake/SKILL.md        (constructs CONTENT_BRIEF; sets format: blog)
  → router/SKILL.md        (reads CONTENT_BRIEF.format = blog)
  → blog/SKILL.md          (generation skill; selects subtype; loads blog-templates)
  → humanise/SKILL.md      (final linguistic pass)
```

---

## What Happens After The Command Runs

1. The intake skill opens and runs the full intake sequence for blog content.
2. Intake constructs the CONTENT_BRIEF and sets `CONTENT_BRIEF.format` to `blog`.
3. The router reads `CONTENT_BRIEF.format`, confirms it as `blog`, and activates `skills/blog/SKILL.md`.
4. The blog generation skill determines the correct subtype from `CONTENT_BRIEF.sub_type` and loads the matching template from `skills/blog-templates/SKILL.md`.
5. The humanise skill runs the final linguistic pass appropriate to the blog subtype.

---

## Output Behaviour

The output is a fully generated blog post in the format and subtype determined during intake. Output format defaults to `markdown` unless `CONTENT_BRIEF.output_format` specifies otherwise.

---

## Restrictions

This command must NOT:

- Generate blog content directly
- Ask intake questions
- Construct or modify CONTENT_BRIEF
- Select or infer a blog subtype
- Load or apply templates
- Apply SEO logic
- Apply humanise logic
- Invoke sector overlays

All logic resides inside the skills. This command is a pipeline trigger only.
