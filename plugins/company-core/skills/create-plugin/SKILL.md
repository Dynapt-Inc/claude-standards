---
name: create-plugin
description: Standards for contributing plugins, skills, agents, and hooks to the Dynapt claude-standards repository. Use when a contributor asks how to add a standard, skill, rule, command, or agent to this repo, or when authoring or validating a new plugin.
---

# Plugin Authoring Standards

This repo is a Claude Code plugin marketplace. Every standard, rule, or workflow
lives as a **skill** inside a plugin under `plugins/`.

## Plugin directory layout

```
plugins/<plugin>/
├── .claude-plugin/
│   └── plugin.json        # manifest (name is the only required field)
├── skills/                # one subdirectory per skill
│   └── <skill-name>/
│       ├── SKILL.md       # required
│       └── reference.md   # optional long-form detail
├── agents/                # optional subagent .md files
├── hooks/
│   └── hooks.json         # optional event handlers
├── .mcp.json              # optional MCP servers
└── scripts/               # optional hook/utility scripts
```

> Do NOT create a `commands/` directory — that format is legacy. Use `skills/` only.
> Only `plugin.json` belongs inside `.claude-plugin/`. All other dirs go at plugin root.

## Which plugin does this belong in?

| Domain                              | Plugin         |
| ----------------------------------- | -------------- |
| Cross-cutting / applies to everyone | `company-core` |
| Writing code, PRs, testing, git     | `engineering`  |
| System design, ADRs, tech decisions | `architecture` |
| Content, SEO, social, campaigns     | `marketing`    |
| PRDs, user stories, release notes   | `product`      |
| SQL, analysis, dashboards           | `data`         |

## Naming standard

Plugin and skill names must describe the **action performed**, not the subject area or concept.

Use a verb or verb phrase that answers "what does this do right now?"

| Bad (concept/noun) | Good (action/verb) |
|---|---|
| `plugin-authoring` | `create-plugin` |
| `code-standards` | `review-code` |
| `branch-workflow` | `create-branch` |
| `seo` | `optimize-seo` |
| `onboarding` | `onboard-employee` |
| `release` | `write-release-notes` |

**Rules:**
- Start with a verb: `create-`, `review-`, `write-`, `generate-`, `analyze-`, `optimize-`, `run-`, etc.
- Kebab-case, no spaces
- Specific enough that two people independently name the same skill the same thing

## Adding a skill

1. Create `plugins/<plugin>/skills/<skill-name>/` (kebab-case).
2. Add `SKILL.md`:

```markdown
---
name: skill-name
description: Third-person. WHAT it does + WHEN Claude should activate it.
  Include explicit trigger phrases ("Use when reviewing pull requests or
  when the user mentions code review").
---

# Skill Title

## Instructions

...your standard/rule/workflow...

## Additional resources

- [reference.md](reference.md)
```

3. Keep `SKILL.md` under ~300 lines. Long reference material goes in a sibling
   file (e.g. `reference.md`) — link to it, don't inline it.
4. Remove `example-skill/` from a plugin once you have real skills.

### Description rules (critical)

The description controls auto-activation. Write it carefully.

- **Third person** — "Reviews pull requests..." not "I can review..."
- **Include WHAT and WHEN** with explicit trigger keywords

```
# Good
Enforces Dynapt code review standards. Use when reviewing a pull request,
examining a diff, or when the user asks for a code review.

# Bad
Helps with code.
```

## Adding an agent

Create `plugins/<plugin>/agents/<agent-name>.md`:

```markdown
---
name: agent-name
description: What this agent specializes in and when Claude should invoke it
model: sonnet
effort: medium
maxTurns: 20
disallowedTools: Write, Edit
---

Detailed system prompt describing the agent's role and behavior.
```

Supported frontmatter fields: `name`, `description`, `model`, `effort`,
`maxTurns`, `tools`, `disallowedTools`, `skills`, `memory`, `background`,
`isolation` (only valid value: `"worktree"`).

Not supported in plugin agents: `hooks`, `mcpServers`, `permissionMode`.

## plugin.json manifest

`name` is the only required field. Full schema:

```json
{
  "name": "plugin-name",
  "displayName": "Human Readable Name",
  "version": "1.2.0",
  "description": "Brief plugin description",
  "author": { "name": "...", "email": "..." },
  "homepage": "https://...",
  "repository": "https://github.com/...",
  "license": "MIT",
  "keywords": ["keyword1"]
}
```

Do not add component path fields (`skills`, `commands`, `agents`) unless you
need to override default locations — and if you do, all paths must start with
`./`. Adding component paths when `strict: false` is set in `marketplace.json`
will cause a conflict error.

## Versioning (important)

**Because `version` is set in each `plugin.json`, you MUST bump it for
teammates to receive your changes.** Pushing commits alone is not enough —
Claude Code uses the version string as the cache key.

```json
"version": "1.1.0"
```

Follow semver:

- `PATCH` — wording fixes, reference file changes
- `MINOR` — new skills or agents
- `MAJOR` — renamed/removed skills (breaking for namespaced invocations)

If you're iterating rapidly, you can temporarily remove `version` from
`plugin.json` so every commit is treated as a new version (git SHA is used).

## Path rules

- All custom paths in `plugin.json` must be relative and start with `./`
- No path traversal outside the plugin directory (`../` won't work after caching)
- `skills` field **adds to** the default `skills/` scan; it doesn't replace it
- `commands`, `agents` fields **replace** the default directory scan

## Validating your plugin

```bash
claude plugin validate ./plugins/<plugin-name>

# Strict mode — treats warnings (e.g. unrecognised fields) as errors
claude plugin validate ./plugins/<plugin-name> --strict
```

Run this before committing. Common errors:

- `name: Required` — missing name in plugin.json
- `No commands found in ... Expected .md files or SKILL.md in subdirectories` — empty or wrong-format skill dir
- `conflicting manifests` — component paths set in both plugin.json and marketplace.json when strict: false

For deeper debugging: `claude --debug`

## Hooks (optional)

Add `hooks/hooks.json` to the plugin root. Always use `${CLAUDE_PLUGIN_ROOT}`
for script paths and wrap it in quotes:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "\"${CLAUDE_PLUGIN_ROOT}\"/scripts/validate.sh"
          }
        ]
      }
    ]
  }
}
```

Hook types: `command`, `http`, `mcp_tool`, `prompt`, `agent`.
Hook scripts must be executable (`chmod +x`).

## After adding content

1. Bump `version` in `plugins/<plugin>/.claude-plugin/plugin.json`.
2. Commit and push.
3. Teammates run: `claude plugin update <plugin>@claude-standards`

## See also

- [Plugins reference](https://code.claude.com/docs/en/plugins-reference)
- [Skills](https://code.claude.com/docs/en/skills)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Hooks](https://code.claude.com/docs/en/hooks)
