# Contributing standards

This repo is a Claude Code plugin marketplace. Each "standard", "rule", or
"skill" is a folder containing a `SKILL.md` file inside the relevant plugin.

## Add a skill

1. Pick the right plugin under `plugins/` (or add a new one — see below).
2. Create a folder in that plugin's `skills/` directory, kebab-case named:
   `plugins/engineering/skills/code-review/`.
3. Add a `SKILL.md` with YAML frontmatter:

   ```markdown
   ---
   name: code-review
   description: Third-person WHAT + WHEN with trigger keywords. Used by Claude to decide when to auto-activate.
   ---

   # Code Review

   ...your standard/rule content...
   ```

4. Keep `SKILL.md` focused (aim under ~500 lines). Put long reference material in
   sibling files (e.g. `reference.md`) and link to them.
5. Bump the `version` in that plugin's `.claude-plugin/plugin.json` so installed
   teammates receive the update.

## Add slash commands or agents (optional)

- Slash commands: add `.md` files to the plugin's `commands/` directory.
- Subagents: add `.md` files to the plugin's `agents/` directory.

## Add a new plugin (new team/domain)

1. Create `plugins/<name>/.claude-plugin/plugin.json` (copy an existing one).
2. Create `plugins/<name>/skills/` and add at least one skill.
3. Register it in `.claude-plugin/marketplace.json` under `plugins`.

## Naming conventions

- Plugins, skills, commands, agents: **kebab-case**.
- Descriptions: third person, include trigger keywords so skills activate at the
  right time.

## Before you commit

- Validate JSON: every `plugin.json` and `marketplace.json` must be valid JSON.
- Each skill folder must contain a `SKILL.md`.
