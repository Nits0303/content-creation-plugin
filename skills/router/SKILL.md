# Skill: Router
File: skills/router/SKILL.md  
Role: Route execution to the correct generation skill based on the CONTENT_BRIEF.

The router skill does not ask questions and does not generate content.

Its responsibility is to read the populated CONTENT_BRIEF and direct execution to the correct skill for content generation.

---

# Skill Purpose

The router sits between the intake system and the generation skills.

The intake skill gathers signals and constructs the CONTENT_BRIEF.  
Once intake is complete, execution passes to the router.

The router reads the CONTENT_BRIEF and determines which generation skill should run.

---

# Routing Logic

The router reads the following fields:

- `format`
- `sub_type` (reserved for future subtype-level routing)

The primary routing decision is made using `format`.

Supported format values:

- `blog`
- `webpage`
- `social`

---

# Routing Map

| Format | Destination Skill |
|------|------|
| blog | skills/blog/SKILL.md |
| webpage | skills/webpage/SKILL.md |
| social | skills/social/SKILL.md |

---

# Behaviour Rules

• The router does not modify the CONTENT_BRIEF.  
• The router does not ask questions.  
• The router does not generate content.  
• The router simply transfers execution to the appropriate skill.

---

# Execution

When the router receives the CONTENT_BRIEF:

1. Read `format`.
2. Match the format to the routing map.
3. Pass the CONTENT_BRIEF unchanged to the selected skill.

---

# Fallback Safety

If the `format` field is missing or invalid:

1. Return control to the intake skill.
2. Request clarification of the content type.

Example clarification question:

> "Are we creating a blog post, a webpage, or social media content?"

When control returns to intake, intake resumes at **format clarification only**.  
Existing CONTENT_BRIEF fields must be preserved.

The router must not proceed without a valid format.

---

# Handoff

After routing, execution moves entirely to the selected generation skill.

The router does not participate in the generation process after handoff.