# /create

Primary command for generating content.

This command launches the structured intake system used by the content creation architecture.  
The intake system gathers the information required to build a structured **CONTENT_BRIEF** and automatically determines the correct content format.

Formats supported:
- Blog
- Webpage
- Social Media

Users do not need to specify the format. The system extracts this from the user's answer.

---

## Workflow

1. Context extraction (if URLs or documents are provided)
2. Opening intake question
3. Routing checkpoint (if subtype ambiguity exists)
4. Universal brief questions (Phase 2)
5. Subtype-specific questions (Phase 3)
6. Optional SEO layer
7. Brief validation
8. Content generation

The system asks questions one at a time and skips any signal already provided.  
The intake process progressively builds the **CONTENT_BRIEF** used for generation.

---

Fast-lane commands are also available:
- /create-blog
- /create-page
- /create-social

These skip the format classification step and start directly at format-specific intake.