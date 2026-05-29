---
name: example-skill
description: TEMPLATE — replace this. Describe in third person WHAT this skill does and WHEN Claude should use it, including trigger keywords (e.g. "Use when reviewing pull requests or when the user mentions code review").
---

# Example Skill (template)

This is a placeholder so you can see the required format. To add one of your
existing standards/skills:

1. Copy this `example-skill/` folder and rename it (kebab-case, e.g. `code-review`).
2. Update the `name` and `description` in the frontmatter above.
3. Replace this body with your standard/rule content.
4. Delete this `example-skill/` folder once you have real skills.

Skills auto-activate based on the `description`. Once installed, this skill is
also invocable explicitly as `/company-core:example-skill`.

## Instructions

Put the actual guidance, rules, or workflow here.

## Additional resources

For long reference material, add sibling files (e.g. `reference.md`) and link to
them from here — keep `SKILL.md` focused.
