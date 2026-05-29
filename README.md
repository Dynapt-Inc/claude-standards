# Claude Standards

Company-wide standards, skills, and rules for using Claude — packaged as a
[Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces)
so anyone on the team can plug them straight into Claude with two commands.

Each team installs the plugins relevant to them. Skills auto-activate based on
context; slash commands and agents are invoked explicitly.

## Install (for everyone on the team)

In Claude Code:

```
/plugin marketplace add Dynapt-Inc/claude-standards
/plugin install company-core@claude-standards
```

Then install whichever team plugins you need:

```
/plugin install engineering@claude-standards
/plugin install architecture@claude-standards
/plugin install marketing@claude-standards
/plugin install product@claude-standards
/plugin install data@claude-standards
```

> Replace `Dynapt-Inc/claude-standards` with the real GitHub `org/repo` once this
> is pushed. It appears here and in `.claude-plugin/marketplace.json`
> + each `plugins/*/.claude-plugin/plugin.json` (author email).

## Available plugins

| Plugin | What it covers |
| --- | --- |
| `company-core` | Baseline everyone installs: brand voice, communication standards, security & confidentiality rules, shared glossary. |
| `engineering` | Coding standards, code review, testing, git/PR workflow, secure coding. |
| `architecture` | System design, architecture decision records (ADRs), technology selection. |
| `marketing` | Content writing, SEO, campaign briefs, social copy. |
| `product` | PRDs, user stories, release notes. |
| `data` | SQL standards, analysis methodology, dashboard/reporting conventions. |

## Repository layout

```
claude-standards/
├── .claude-plugin/
│   └── marketplace.json        # Registry of all plugins (repo root)
└── plugins/
    └── <plugin>/
        ├── .claude-plugin/
        │   └── plugin.json     # Plugin manifest
        ├── skills/             # Skills — each in its own folder with SKILL.md
        │   └── <skill>/SKILL.md
        ├── commands/           # Optional slash commands (.md files)
        └── agents/             # Optional subagent definitions (.md files)
```

## Adding your standards

See [CONTRIBUTING.md](CONTRIBUTING.md). In short: drop each standard/rule into a
skill folder under the right plugin's `skills/` directory, then bump that
plugin's `version` in its `plugin.json` so teammates get the update.

## Using these outside Claude Code

The skills are plain Markdown, so you can also reuse them in Claude.ai Projects
or via the API: paste the relevant `SKILL.md` contents into a Project's custom
instructions or your system prompt. The marketplace install above is the
recommended path for Claude Code users.
