---
name: create-plugin
description: Enforces Dynapt standards for creating a new plugin in this repo. Use when a contributor asks how to create a plugin, add a new plugin folder, register a plugin in the marketplace, or scaffold a new team's plugin.
when_to_use: Triggers on "new plugin", "add plugin", "create plugin", "register plugin", "scaffold plugin", "add a team plugin".
---

# Create a Plugin

## Naming rule (enforced)

Plugin folder names and `name` in `plugin.json` must be an **action-oriented
noun** describing the team or domain — short, lowercase, kebab-case.

| Bad | Good |
|---|---|
| `engineering-standards` | `engineering` |
| `my-teams-plugin` | `data` |
| `claudeRules` | `marketing` |

---

## Step 1 — Scaffold the directory

```
plugins/<plugin-name>/
├── .claude-plugin/
│   └── plugin.json     ← manifest
├── skills/             ← add skills here (see create-skill)
└── agents/             ← optional subagent .md files
```

Rules:
- Only `plugin.json` goes inside `.claude-plugin/`. Everything else at plugin root.
- Do NOT create a `commands/` directory — that format is legacy.
- Do NOT add `hooks/`, `agents/`, `.mcp.json` unless the plugin actually uses them.

---

## Step 2 — Write plugin.json

`name` is the only required field. Use this template:

```json
{
  "name": "plugin-name",
  "displayName": "Human Readable Name",
  "version": "1.0.0",
  "description": "One sentence description.",
  "author": {
    "name": "Caleb Lawson",
    "email": "caleb.lawson@dynapt.com"
  },
  "keywords": ["keyword1", "keyword2"]
}
```

Do NOT add `skills`, `commands`, or `agents` path fields unless overriding
default locations. All custom paths must start with `./`. Adding component
paths when `strict: false` is set in `marketplace.json` causes a conflict error.

---

## Step 3 — Register in marketplace.json

Add an entry to `.claude-plugin/marketplace.json` under `plugins`:

```json
{
  "name": "plugin-name",
  "source": "./plugins/plugin-name",
  "description": "One sentence description.",
  "category": "category",
  "tags": ["tag1", "tag2"],
  "strict": false
}
```

Valid categories: `core`, `engineering`, `marketing`, `product`, `data`.

---

## Step 4 — Add at least one skill

Every plugin must have at least one real skill before merging.
See the [create-skill](../create-skill/SKILL.md) standard for how to write it.

---

## Versioning (enforced)

Because `version` is set in `plugin.json`, **you MUST bump it for teammates
to receive your changes.** Pushing commits without bumping has no effect —
Claude Code uses the version string as the cache key.

Follow semver:
- `PATCH` — wording/reference file fixes
- `MINOR` — new skills or agents added
- `MAJOR` — skills renamed or removed (breaks namespaced invocations)

To iterate quickly without bumping, temporarily remove `version` from
`plugin.json` — Claude Code falls back to the git commit SHA.

---

## Validating your plugin

```bash
claude plugin validate ./plugins/<plugin-name>

# Strict mode — warnings become errors
claude plugin validate ./plugins/<plugin-name> --strict
```

Run before committing. Common errors:
- `name: Required` — missing name in plugin.json
- `Plugin directory not found` — wrong `source` path in marketplace.json
- `conflicting manifests` — component path fields set in both plugin.json and marketplace.json with `strict: false`

---

## Optional: agents

Add `.md` files to `agents/`:

```markdown
---
name: agent-name
description: What this agent does and when Claude should invoke it
model: sonnet
effort: medium
maxTurns: 20
disallowedTools: Write, Edit
---

System prompt for the agent.
```

Supported frontmatter: `name`, `description`, `model`, `effort`, `maxTurns`,
`tools`, `disallowedTools`, `skills`, `memory`, `background`, `isolation`
(`"worktree"` only).

Not supported in plugin agents: `hooks`, `mcpServers`, `permissionMode`.

---

## See also

- [create-skill](../create-skill/SKILL.md) — how to write skills inside a plugin
- [Plugins reference](https://code.claude.com/docs/en/plugins-reference)
