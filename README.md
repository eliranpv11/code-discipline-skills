# Code Discipline

> *A coding methodology for Claude Code that enforces structured thinking, surgical changes, and verifiable success criteria.*

---

## The Problem

Left unconstrained, Claude Code exhibits predictable failure modes when writing code:

- **Silent assumptions** — fills in missing information without flagging it, then runs with the wrong interpretation
- **Overengineering** — reaches for abstraction, flexibility, and configurability that wasn't asked for
- **Scope drift** — refactors adjacent code "while there", touches things unrelated to the task
- **Weak success criteria** — marks tasks done without verifiable proof they work
- **Point-fixes** — patches the symptom without understanding what the code depends on

Code Discipline exists to eliminate all of them.

---

## What Code Discipline owns

Code Discipline is a **coding methodology layer**. It controls:

| Area | What it enforces |
|---|---|
| Before coding | Present assumptions, risks, options, and blast radius |
| Code quality | Simplicity, modularity, no magic numbers, no silent failures |
| Change scope | Touch only what the request requires — nothing adjacent |
| Success criteria | Every task has a verifiable definition of done |
| Priority order | Stability → Reliability → Readability → ... → Elegance |

It does **not** own: honesty in reporting, documentation standards, or mistake handling — those belong to **truth-serum**.

---

## Before & After

| Without Code Discipline | With Code Discipline |
|---|---|
| Makes assumptions silently | States assumptions before touching code |
| Picks one solution without asking | Presents options when multiple exist |
| Writes 300 lines for a 30-line problem | Asks: "Would a senior engineer say this is overcomplicated?" |
| Refactors adjacent code "while there" | Touches only what the request requires |
| No definition of done | Every task has a verifiable success criterion |
| Tests that match mocks, not reality | Tests verified to assert real-world behavior |

---

## The Four Principles

| # | Principle | What It Prevents |
|---|---|---|
| 1 | **Think Before Coding** | Silent assumptions, unasked questions, surprise blast radius |
| 2 | **Simplicity First** | Overengineering, speculative abstractions, bloated code |
| 3 | **Surgical Changes** | Scope creep, unrelated refactors, "while I'm here" edits |
| 4 | **Goal-Driven Execution** | Weak success criteria, untestable claims, vague completion |

Plus: a mandatory prohibitions table, mandatory review questions before each step, and a priority order for every technical decision.

---

## Priority Order

When decisions conflict, this order applies:

1. Stability
2. Reliability
3. Readability
4. Maintainability
5. Scalability
6. Performance
7. Elegance

---

## Installation

### Option A — Claude Code Plugin (recommended)

In a Claude Code session, run:

```
/plugin marketplace add eliranpv11/code-discipline-skills
/plugin install code-discipline@code-discipline
```

The skill is installed globally across all your Claude Code sessions immediately.

---

### Option B — Claude.ai (Web & Desktop)

1. Go to **claude.ai → Customize → Skills**
2. Click **+** → **Create skill**
3. Paste the contents of [`SKILL.md`](./SKILL.md) into the skill editor
4. Name it `code-discipline` and save
5. Toggle it on ✅

---

### Option C — Personal install (all your projects)

Install once, available across all local Claude Code projects:

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/eliranpv11/code-discipline-skills.git \
  ~/.claude/skills/code-discipline
```

---

### Option D — Project install (shared with team)

Install in a project repo so the whole team uses it:

```bash
mkdir -p .claude/skills
git clone https://github.com/eliranpv11/code-discipline-skills.git \
  .claude/skills/code-discipline
git add .claude/skills/
git commit -m "chore: add Code Discipline skill"
```

---

### Option E — One-liner (no git required)

Download just the skill file directly:

```bash
mkdir -p ~/.claude/skills/code-discipline && \
  curl -o ~/.claude/skills/code-discipline/SKILL.md \
  https://raw.githubusercontent.com/eliranpv11/code-discipline-skills/main/SKILL.md
```

---

### Option F — CLAUDE.md paste (zero-install, works everywhere)

The `CLAUDE.md` file contains the complete skill content without YAML frontmatter — ready to paste directly into any project.

1. Copy the contents of [`CLAUDE.md`](./CLAUDE.md)
2. Paste into your project's `CLAUDE.md` file (merge with existing content if needed)

This approach requires no installation and works in any environment that reads `CLAUDE.md`.

---

### Option G — Cursor IDE

1. Copy [`.cursor/rules/code-discipline.mdc`](./.cursor/rules/code-discipline.mdc) to your project's `.cursor/rules/` directory
2. Cursor will apply the rule automatically on every request (`alwaysApply: true`)

Or install manually:

```bash
mkdir -p .cursor/rules
curl -o .cursor/rules/code-discipline.mdc \
  https://raw.githubusercontent.com/eliranpv11/code-discipline-skills/main/.cursor/rules/code-discipline.mdc
```

---

## How to Use

Once installed, Code Discipline applies automatically. Claude will surface assumptions before coding, stay within scope, and define verifiable success criteria for every task.

You can also invoke it explicitly:

> "Apply code-discipline to this task."
> "Use the code-discipline principles for this review."

---

## Verify Installation

Start a Claude Code session and ask:

```
What coding methodology are you following?
```

Claude should describe the four principles, the priority order, the mandatory review questions, and the prohibitions table.

---

## The Skill Suite

Code Discipline is part of a three-skill suite — each owns a distinct layer:

| Skill | Layer | Use when |
|---|---|---|
| **[truth-serum](https://github.com/eliranpv11/claude-code-truth-serum-skill)** | Honesty & communication | You need Claude to report reality accurately |
| **code-discipline** | Coding methodology | You need Claude to write code the right way |
| **[autonomous-agent-protocol](https://github.com/eliranpv11/autonomous-agent-protocol-skill)** | Execution framework | Claude runs without mid-task human approvals |

Load all three for the complete stack. Load any one standalone — each works independently.

---

## Compatibility

- ✅ Claude Code (CLI)
- ✅ Claude.ai (Web & Desktop)
- ✅ Cursor IDE
- ✅ Any agent supporting the Agent Skills open standard

---

## File Structure

```
code-discipline-skills/
├── SKILL.md                        ← Install as a Claude Code skill
├── CLAUDE.md                       ← Copy into any project's CLAUDE.md
├── README.md                       ← This file
├── EXAMPLES.md                     ← Real before/after examples
├── LICENSE                         ← MIT
├── .claude-plugin/
│   ├── plugin.json                 ← Plugin metadata
│   └── marketplace.json            ← Marketplace listing
└── .cursor/
    └── rules/
        └── code-discipline.mdc     ← Cursor IDE rules (alwaysApply: true)
```

---

## License

MIT — see [LICENSE](LICENSE)
