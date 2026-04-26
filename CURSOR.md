# Using Code Discipline with Cursor

This repository ships with a committed Cursor project rule so the Code Discipline methodology applies automatically when you work inside this folder. This document covers everything you need to know to use it here, replicate it in another project, troubleshoot, and keep it in sync as a contributor.

---

## How It Works in This Repository

1. The rule lives at [`.cursor/rules/code-discipline.mdc`](.cursor/rules/code-discipline.mdc).
2. It declares `alwaysApply: true` in its frontmatter, which means Cursor injects it on **every** request inside this project — no manual activation required.
3. Open the folder in Cursor and the rule is active immediately. You do not need to run any installation step.
4. To confirm it is loaded: open **Cursor Settings → Rules** (or the project rules UI). The entry `code-discipline` should appear.

---

## Using the Same Rule in Another Project

The rule is a single self-contained file. To apply Code Discipline to a different project under Cursor:

```bash
mkdir -p .cursor/rules
cp /path/to/code-discipline-skills/.cursor/rules/code-discipline.mdc \
   .cursor/rules/code-discipline.mdc
```

Or download it directly without cloning the repository:

```bash
mkdir -p .cursor/rules
curl -o .cursor/rules/code-discipline.mdc \
  https://raw.githubusercontent.com/eliranpv11/code-discipline-skills/main/.cursor/rules/code-discipline.mdc
```

Once the file is in place, Cursor activates it on the next request — no restart needed.

---

## Merging With Existing Cursor Rules

If your project already has a `.cursor/rules/` directory with other rules, **add Code Discipline alongside them — do not overwrite**.

Cursor applies all rules in the directory whose `alwaysApply` is `true`, and merges them into the system context. There is no conflict between Code Discipline and project-specific rules unless they directly contradict each other.

If a contradiction exists (for example, a project rule says "always add type hints" and Code Discipline says "match existing style"):
- The project-specific rule takes precedence for project-specific conventions.
- Code Discipline still applies to everything else.

You can also customize Code Discipline locally by editing the copied `.mdc` file. The original repository is the canonical version — local edits are your call.

---

## Cursor vs. Claude Code: When to Use Which

| Surface | Use this skill format |
|---|---|
| **Cursor IDE** | `.cursor/rules/code-discipline.mdc` (committed in repo or copied) |
| **Claude Code (CLI)** | The plugin installed via `/plugin install` |
| **Claude.ai (Web/Desktop)** | The skill content pasted into a custom skill |
| **Any project, any tool** | The contents of `CLAUDE.md` pasted into your project root |

Cursor reads `.cursor/rules/*.mdc` files automatically.
Cursor does **not** read `.claude-plugin/`, `skills/`, or `CLAUDE.md` by default — those are for other surfaces.

If you want the same methodology to apply across both Cursor and Claude Code in the same project, install both:
1. Drop `.cursor/rules/code-discipline.mdc` for Cursor.
2. Either install the Claude Code plugin globally, or paste `CLAUDE.md` content into the project's `CLAUDE.md` for Claude Code.

The two integrations do not interfere with each other.

---

## Frontmatter Reference

The `.mdc` file uses Cursor's standard frontmatter format:

```yaml
---
description: <one-line summary used by Cursor for indexing>
alwaysApply: true
---
```

| Field | Purpose |
|---|---|
| `description` | Short summary shown in the Cursor rules UI. Also used for context indexing. |
| `alwaysApply: true` | The rule is injected on every request. Set to `false` to make it manual-only. |

If you want Cursor to load the rule **only when explicitly invoked** (instead of on every request), change `alwaysApply: true` to `alwaysApply: false` in your local copy. The full text of the methodology stays the same — only the activation mode changes.

---

## Troubleshooting

### The rule does not appear in Cursor's rules UI

- Confirm the file is named exactly `code-discipline.mdc` (case-sensitive on macOS/Linux).
- Confirm it sits under `.cursor/rules/` at the project root, not nested deeper.
- Reload the Cursor window (Command Palette → `Developer: Reload Window`).

### The rule is listed but its content is not applied

- Open the file and confirm the frontmatter starts and ends with `---` on their own lines.
- Confirm `alwaysApply: true` is present (no quotes, exact spelling).
- Confirm there are no YAML syntax errors — a missing colon or unclosed quote will silently disable the rule.

### The methodology applies in some files but not others

- `alwaysApply: true` rules apply at the project level. If behavior is inconsistent across files in the same project, the file is likely outside the project root Cursor opened. Check Cursor's "Workspace" indicator.

### A rule contradicts the methodology

- Local project rules win for local conventions. If you want Code Discipline to fully override, remove or comment out the conflicting rule in `.cursor/rules/`.

---

## For Contributors

If you change the four principles or any other content shared across surfaces, update **all** of the following so they stay in sync:

| File | Purpose |
|---|---|
| [`CLAUDE.md`](CLAUDE.md) | Zero-install, paste-into-any-project version |
| [`.cursor/rules/code-discipline.mdc`](.cursor/rules/code-discipline.mdc) | Cursor IDE rule |
| [`skills/code-discipline/SKILL.md`](skills/code-discipline/SKILL.md) | Claude Code plugin / Claude.ai skill |

The six principles, priority order, mandatory prohibitions, and review questions must be identical across all three. If you make a non-trivial change to one, update the other two before merging.

The version numbers in `.claude-plugin/marketplace.json`, `.claude-plugin/plugin.json`, and `skills/code-discipline/SKILL.md` (`metadata.version`) must also match. Bump them together.
