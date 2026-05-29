---
name: plugin-authoring
description: Standards for contributing plugins, skills, commands, and agents to the Dynapt claude-standards repository. Use when a contributor asks how to add a standard, skill, rule, command, or agent to this repo, or when authoring a new plugin.
---

# Plugin Authoring Standards

This repo is a Claude Code plugin marketplace. Every standard, rule, or workflow
lives as a **skill** (or command/agent) inside a plugin under `plugins/`.

## Repo layout

```
.claude-plugin/
  marketplace.json          ← registry of all plugins
plugins/
  <plugin>/
    .claude-plugin/
      plugin.json           ← plugin manifest
    skills/
      <skill>/
        SKILL.md            ← required; one per skill
        reference.md        ← optional long-form detail
    commands/               ← optional slash commands (.md files)
    agents/                 ← optional subagent definitions (.md files)
```

## Which plugin does this belong in?

| Domain | Plugin |
|---|---|
| Applies to everyone / cross-cutting | `company-core` |
| Writing code, PRs, testing, git | `engineering` |
| System design, ADRs, tech decisions | `architecture` |
| Content, SEO, social, campaigns | `marketing` |
| PRDs, user stories, release notes | `product` |
| SQL, analysis, dashboards | `data` |

If it spans multiple domains, put it in `company-core`.

## Adding a skill

1. Create a folder under `plugins/<plugin>/skills/<skill-name>/` (kebab-case).
2. Add a `SKILL.md` with this structure:

```markdown
---
name: skill-name
description: Third-person. WHAT it does + WHEN Claude should activate it.
  Include trigger keywords (e.g. "Use when reviewing pull requests or when
  the user mentions code review").
---

# Skill Title

## Instructions
...your standard/rule/workflow...

## Additional resources
- [reference.md](reference.md)  ← link here; don't inline long content
```

3. Keep `SKILL.md` under ~300 lines. Move long reference material to a sibling
   `reference.md` and link to it.
4. Delete `example-skill/` from the plugin once you have real skills.

## Description rules (critical)

The description drives auto-activation — write it carefully.

- **Third person**: "Reviews pull requests..." not "I can review..."
- **Include WHAT and WHEN**: what Claude does + explicit trigger phrases
- **Include keywords** people will actually say or type

Good:
```
Enforces Dynapt code review standards. Use when reviewing a pull request,
examining a diff, or when the user asks for a code review.
```

Bad:
```
Helps with code.
```

## Naming conventions

- Plugin names: kebab-case, matches the folder name, matches `name` in `plugin.json`
- Skill names: kebab-case, matches the folder name, matches `name` in frontmatter
- Command files: kebab-case `.md` files in `commands/`
- Agent files: kebab-case `.md` files in `agents/`

## Skills vs commands vs agents

| Use | When |
|---|---|
| **Skill** | Domain knowledge or standards Claude draws on automatically (most things go here) |
| **Command** | A specific workflow a user explicitly triggers via `/plugin:command` |
| **Agent** | A specialist role that orchestrates multi-step tasks |

Default to skills. Promote to a command only if it needs explicit invocation.

## After adding content

1. Bump `version` in `plugins/<plugin>/.claude-plugin/plugin.json` (semver).
2. Commit and push — teammates run `/plugin update @claude-standards` to receive changes.

## Versioning

```json
"version": "1.2.0"
```

- Patch (`1.0.x`): fix wording, add reference files
- Minor (`1.x.0`): add new skills or commands
- Major (`x.0.0`): rename/remove skills (breaking for anyone relying on namespaced invocation)
