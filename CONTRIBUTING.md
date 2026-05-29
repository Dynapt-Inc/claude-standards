# Contributing standards

This repo is a Claude Code plugin marketplace. Each standard, rule, or workflow
is a skill folder inside the relevant plugin under `plugins/`.

## Quick start

The `company-core` plugin ships a `plugin-authoring` skill that guides Claude
through these steps. If you have the plugin installed, just say "I want to add a
standard for X" and Claude will follow the rules automatically.

## Plugin layout

```
plugins/<plugin>/
├── .claude-plugin/
│   └── plugin.json        # manifest — name is the only required field
├── skills/                # one directory per skill
│   └── <skill-name>/
│       ├── SKILL.md       # required
│       └── reference.md   # optional — link to from SKILL.md for long content
├── agents/                # optional subagent .md files
├── hooks/
│   └── hooks.json         # optional event handlers
└── scripts/               # optional hook/utility scripts
```

> `commands/` (legacy flat .md format) is not used here. Use `skills/` only.
> Only `plugin.json` belongs inside `.claude-plugin/`. Everything else goes at
> the plugin root.

## Which plugin?

| Domain | Plugin |
|---|---|
| Cross-cutting / applies to everyone | `company-core` |
| Writing code, PRs, testing, git | `engineering` |
| System design, ADRs, tech decisions | `architecture` |
| Content, SEO, social, campaigns | `marketing` |
| PRDs, user stories, release notes | `product` |
| SQL, analysis, dashboards | `data` |

## Adding a skill

1. Create `plugins/<plugin>/skills/<skill-name>/` (kebab-case).
2. Add `SKILL.md` with frontmatter + body (see `example-skill/` for the format).
3. Keep `SKILL.md` focused — aim under ~300 lines. Move long reference material
   to a sibling `reference.md` and link to it.
4. Delete `example-skill/` once the plugin has real skills.

### Skill frontmatter

```markdown
---
name: skill-name
description: Third-person WHAT + WHEN with trigger keywords.
---
```

The `description` drives auto-activation. Include explicit trigger phrases
("Use when reviewing pull requests or when the user mentions code review").

## After adding content

1. **Bump `version`** in `plugins/<plugin>/.claude-plugin/plugin.json` (semver).
   This is required — Claude Code uses the version string as the cache key.
   Pushing commits without bumping the version has no effect for installed users.
2. Commit and push.
3. Teammates update with: `claude plugin update <plugin>@claude-standards`

## Validating your plugin

```bash
claude plugin validate ./plugins/<plugin-name>
claude plugin validate ./plugins/<plugin-name> --strict
```

## Adding a new plugin (new team or domain)

1. Create `plugins/<name>/.claude-plugin/plugin.json` (copy an existing one).
2. Create `plugins/<name>/skills/` with at least one skill.
3. Register it in `.claude-plugin/marketplace.json` under `plugins` with `"strict": false`.

## Naming conventions

All plugin names, skill folder names, agent file names: **kebab-case**.
