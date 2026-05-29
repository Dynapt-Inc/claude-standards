---
name: create-skill
description: Enforces Dynapt standards for creating a SKILL.md file in this repo. Use when a contributor asks how to create a skill, write a SKILL.md, add a new skill, or add a standard to any plugin in this repository.
---

# Create a Skill

## Naming rule (enforced)

Skill folder names and `name` frontmatter must be an **action verb phrase** — what
the skill does right now, not the subject area it belongs to.

| Bad (concept) | Good (action) |
|---|---|
| `code-standards` | `review-code` |
| `seo` | `optimize-seo` |
| `branching` | `create-branch` |
| `releases` | `write-release-notes` |

Start with a verb: `create-`, `review-`, `write-`, `generate-`, `analyze-`,
`optimize-`, `run-`, `check-`, `format-`, `deploy-`, etc.

---

## Directory structure

```
plugins/<plugin>/skills/<skill-name>/
├── SKILL.md          ← required, keep under 500 lines
├── reference.md      ← optional; link here for long content
├── examples.md       ← optional; concrete examples
└── scripts/
    └── helper.sh     ← optional; scripts Claude can execute
```

Only `SKILL.md` is required. Add sibling files for long reference material and
link to them from `SKILL.md` — do not inline everything.

The folder name becomes the slash command: `skills/review-code/SKILL.md` → `/engineering:review-code`.

---

## SKILL.md structure

```markdown
---
name: skill-name
description: Third-person WHAT + WHEN. Include explicit trigger phrases.
when_to_use: Additional trigger phrases and example requests.
---

## Instructions
...concise guidance...

## Additional resources
- [reference.md](reference.md)
```

---

## Frontmatter fields

All fields are optional except `description` is strongly recommended.

| Field | Use |
|---|---|
| `name` | Display label in skill listings. Does NOT change the slash command (that comes from the folder name). |
| `description` | REQUIRED here. What it does + when to trigger. Capped at 1,536 chars combined with `when_to_use`. Put key use case first. |
| `when_to_use` | Extra trigger phrases appended to `description`. Use to add example requests without bloating `description`. |
| `disable-model-invocation: true` | Prevents Claude from auto-loading. Use for side-effect workflows (`deploy`, `send-email`, `commit`) where you must control timing. |
| `user-invocable: false` | Hides from `/` menu. Use for background knowledge Claude loads silently. |
| `allowed-tools` | Tools pre-approved while skill is active. E.g. `Bash(git add *) Bash(git commit *)` |
| `disallowed-tools` | Tools blocked while skill is active. Clears after your next message. |
| `context: fork` | Runs skill in an isolated subagent. Only for task skills with explicit steps, not reference skills. |
| `agent` | Subagent type when `context: fork` is set. Options: `Explore`, `Plan`, `general-purpose`, or any custom agent. |
| `paths` | Glob patterns. Claude auto-loads skill only when working with matching files. E.g. `"*.sql, *.prisma"` |
| `argument-hint` | Autocomplete hint. E.g. `[issue-number]` or `[filename] [format]` |
| `arguments` | Named positional args for `$name` substitution. E.g. `arguments: [component, from, to]` |
| `model` | Override model for this skill's turn only. |
| `effort` | Override effort: `low`, `medium`, `high`, `xhigh`, `max`. |

---

## Invocation control (enforced)

| Behavior | Frontmatter | When to use |
|---|---|---|
| Claude + user can invoke | _(default)_ | Reference content, standards, guidelines |
| User only | `disable-model-invocation: true` | Side effects: deploy, commit, send, publish |
| Claude only | `user-invocable: false` | Background knowledge with no user action |

**Rule: any skill with a side effect MUST have `disable-model-invocation: true`.**

---

## Description rules (enforced)

The `description` field drives auto-activation. Getting this wrong means the
skill never fires or fires on everything.

```
# Enforced pattern:
# [Third-person verb phrase]. Use when [trigger scenario] or when the user [trigger phrases].

# Good
Enforces Dynapt code review standards for pull requests. Use when reviewing a
pull request, examining a diff, or when the user asks for a code review.

# Bad — too vague, no trigger phrases
Helps with code.

# Bad — first person
I can review your code.
```

Combined `description` + `when_to_use` is truncated at **1,536 characters**.
Put the most important use case first.

---

## Dynamic context injection

Use `` !`command` `` to inject live data before Claude sees the skill:

```markdown
## Current diff
!`git diff HEAD`

## Instructions
Summarize the changes above...
```

The command runs at skill load time. Claude only sees the output, not the command.

For multi-line injection use a fenced ` ```! ` block:

````markdown
```!
node --version
git status --short
```
````

Use `${CLAUDE_SKILL_DIR}` to reference scripts bundled with the skill:

```markdown
!`python3 ${CLAUDE_SKILL_DIR}/scripts/analyze.py`
```

---

## Skill body rules (enforced)

- **Under 500 lines.** Skill content stays in context for the whole session — every line is a recurring token cost.
- **State what to do**, not how or why. No narration.
- **Long reference material** → sibling file + link. Never inline it.
- **Examples** → `examples.md` linked from `SKILL.md`.
- **Scripts** → `scripts/` directory. Reference via `${CLAUDE_SKILL_DIR}/scripts/`.

---

## Which plugin?

| Domain | Plugin |
|---|---|
| Cross-cutting / applies to everyone | `company-core` |
| Writing code, PRs, testing, git | `engineering` |
| System design, ADRs, tech decisions | `architecture` |
| Content, SEO, social, campaigns | `marketing` |
| PRDs, user stories, release notes | `product` |
| SQL, analysis, dashboards | `data` |

---

## After adding a skill

1. Delete `example-skill/` if it still exists in the plugin.
2. Bump `version` in `plugins/<plugin>/.claude-plugin/plugin.json` (required — version string is the cache key).
3. Commit and push.

## See also

- [Official skills reference](https://code.claude.com/docs/en/skills)
- [create-plugin](../create-plugin/SKILL.md) — standards for adding a new plugin
