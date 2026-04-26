# Code Discipline

> A coding methodology for Claude Code that enforces structured thinking, surgical changes, and verifiable success criteria.

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

It does **not** own: honesty in reporting, commit gates, documentation standards, or mistake handling — those belong to **truth-serum**.

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

1. Stability → 2. Reliability → 3. Readability → 4. Maintainability → 5. Scalability → 6. Performance → 7. Elegance

---

## Installation

### Option A — Claude Code Plugin (recommended)

```bash
claude mcp install code-discipline
```

### Option B — Claude.ai Web UI

Upload `code-discipline.skill` via **Settings → Skills → Upload skill file**.

### Option C — Personal install (all your projects)

```bash
mkdir -p ~/.claude/skills/code-discipline
cp SKILL.md ~/.claude/skills/code-discipline/SKILL.md
```

### Option D — Project install (shared with team)

```bash
mkdir -p .claude/skills/code-discipline
cp SKILL.md .claude/skills/code-discipline/SKILL.md
```

### Option E — CLAUDE.md (zero-install, works everywhere)

Copy the contents of `CLAUDE.md` into your project's `CLAUDE.md` file.
Merge with any existing project-specific instructions.

---

## How to Use

Once installed, Claude Code will apply the discipline automatically when relevant.
You can also invoke it explicitly:

> "Apply code-discipline to this task."
> "Use the code-discipline principles for this review."

---

## The Skill Suite

Code Discipline is part of a three-skill suite — each owns a distinct layer:

| Skill | Layer | Use when |
|---|---|---|
| **[truth-serum](https://github.com/eliranpv11/claude-code-truth-serum-skill)** | Honesty & communication | You need Claude to report reality accurately |
| **code-discipline** | Coding methodology | You need Claude to write code the right way |
| **[autonomous-agent-protocol](https://github.com/eliranpv11/autonomous-agent-protocol)** | Execution framework | Claude runs without mid-task human approvals |

Load all three for the complete stack. Load any one standalone — each works independently.

---

## What's Inside

```
code-discipline/
├── SKILL.md      ← Install as a Claude Code skill
├── CLAUDE.md     ← Copy into any project's CLAUDE.md
├── README.md     ← This file
└── LICENSE       ← MIT
```

---

## License

MIT — see [LICENSE](LICENSE)
