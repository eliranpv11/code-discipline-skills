# Code Discipline

> A complete discipline for Claude Code that enforces six production-grade principles: structured thinking, surgical changes, goal-driven execution, truth-first reporting, and systematic problem resolution.

English | [Install](#installation)

---

## The Problem

From Andrej Karpathy's observations on LLM coding behavior:

> "The models make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should."

> "They really like to overcomplicate code and APIs, bloat abstractions, don't clean up dead code... implement a bloated construction over 1000 lines when 100 would do."

Beyond overcomplication, production systems require more:

> An LLM that calls something "done" when it isn't. That claims "tests pass" without showing output. That silently expands scope when one fix reveals three more problems. That commits without asking.

**Code Discipline addresses all of it.**

---

## The Solution

Six principles that work together as a complete working method:

| # | Principle | What It Prevents |
|---|---|---|
| 1 | **Think Before Coding** | Silent assumptions, hidden confusion, unasked questions |
| 2 | **Simplicity First** | Overengineering, speculative abstractions, bloated code |
| 3 | **Surgical Changes** | Scope creep, unrelated refactors, "while I'm here" edits |
| 4 | **Goal-Driven Execution** | Weak success criteria, untestable claims, vague completion |
| 5 | **Truth First** | Aspirational status updates, unverified "done", hidden bad news |
| 6 | **Systematic, Not Point-Fix** | Band-aids, unexplored dependencies, expanding silent scope |

Plus operational rules: a prohibitions table, mandatory review questions, documentation standards, archival discipline, and a mistake-handling protocol.

---

## Before & After

| Without Code Discipline | With Code Discipline |
|---|---|
| Makes assumptions silently | States assumptions before touching code |
| Picks one solution without asking | Presents options when multiple exist |
| Writes 300 lines for a 30-line problem | Asks: "Would a senior engineer say this is overcomplicated?" |
| Refactors adjacent code "while there" | Touches only what the request requires |
| Says "done" — but it's partial | Says "partial" — and explains what remains |
| "Tests pass" with no output shown | Pastes actual test output as evidence |
| Fixes the symptom, misses the cause | Maps dependencies before writing a line |
| Commits without asking | Waits for explicit "commit now" |

---

## Installation

### Option A — Claude Code Plugin (recommended)

```bash
claude mcp install code-discipline
```

### Option B — Claude.ai Web UI

Upload `code-discipline.skill` via **Settings → Skills → Upload skill file**.

### Option C — Personal install (available in all your projects)

```bash
cp SKILL.md ~/.claude/skills/code-discipline/SKILL.md
```

### Option D — Project install (shared with your team)

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

For the CLAUDE.md approach, the guidelines are always active for that project.

---

## Principles at a Glance

**1. Think Before Coding** — Surfaces assumptions, presents tradeoffs, maps blast radius before a single line is written. Stops when unclear instead of guessing forward.

**2. Simplicity First** — Delivers the minimum code that solves the problem. No speculative features, no over-abstraction, no clever constructs. Rewrites if the solution is longer than it needs to be.

**3. Surgical Changes** — Touches only what the request requires. Matches existing style. Reports dead code without deleting it. Every changed line traces directly to the task.

**4. Goal-Driven Execution** — Transforms every task into a verifiable goal. States a plan with explicit checkpoints for multi-step work. Never marks done without evidence.

**5. Truth First** — Reports broken as broken, partial as partial, stubbed as stubbed. Every claim requires `file:line` and quoted code. "It should work" is not acceptable evidence.

**6. Systematic, Not Point-Fix** — Maps what depends on X before fixing X. Documents newly discovered issues instead of silently expanding scope. No band-aids without expiry.

---

## Priority Order

When decisions conflict, this order applies:

1. Stability → 2. Reliability → 3. Readability → 4. Maintainability → 5. Scalability → 6. Performance → 7. Elegance

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

## Related Skills

- [**truth-serum**](https://github.com/EP-Media/claude-code-truth-serum-skill) — Focuses on institutional-grade truth-first reporting and evidence discipline for production systems.
- [**autonomous-agent-protocol**](https://github.com/EP-Media/autonomous-agent-protocol) — A 6-phase auditable workflow with 7 safety checks for Claude Code agents running without mid-task human approvals.

**How they fit together:**
- Use `code-discipline` as your baseline for all development work.
- Add `truth-serum` when you need stricter reporting and documentation standards.
- Add `autonomous-agent-protocol` when running Claude Code in fully autonomous mode.

---

## License

MIT — see [LICENSE](LICENSE)
