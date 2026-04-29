# Code Discipline

> *Stop your AI from overengineering, making silent assumptions, and touching code it wasn't asked to touch.
Six principles + mandatory prohibitions for production-grade AI-assisted coding. Works with Claude Code, Cursor, and Codex.*

---

## The Problem

Left unconstrained, AI coding assistants exhibit predictable failure modes:

- **Silent assumptions** — fills in missing information without flagging it, then runs with the wrong interpretation
- **Overengineering** — reaches for abstraction, flexibility, and configurability that wasn't asked for
- **Scope drift** — refactors adjacent code "while there", touches things unrelated to the task
- **Weak success criteria** — marks tasks done without verifiable proof they work
- **Point-fixes** — patches the symptom without understanding what the code depends on

Code Discipline exists to eliminate all of them.

---

## What Code Discipline Enforces

| Area | What it enforces |
|---|---|
| Before coding | Present assumptions, risks, options, and blast radius |
| Code quality | Simplicity, modularity, no magic numbers, no silent failures |
| Change scope | Touch only what the request requires — nothing adjacent |
| Success criteria | Every task has a verifiable definition of done |
| Priority order | Stability → Reliability → Readability → ... → Elegance |

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

## The Six Principles

| # | Principle | What It Prevents |
|---|---|---|
| 1 | **Think Before Coding** | Silent assumptions, unasked questions, surprise blast radius |
| 2 | **Verify Reality Before Acting** | Invented APIs, made-up imports, coding from memory instead of from the codebase |
| 3 | **Simplicity First** | Overengineering, speculative abstractions, bloated code |
| 4 | **Surgical Changes** | Scope creep, unrelated refactors, "while I'm here" edits |
| 5 | **Respect Existing Contracts** | Silent breaking changes, forgotten consumers, undeclared schema and API churn |
| 6 | **Goal-Driven Execution** | Weak success criteria, untestable claims, vague completion |

Plus: a mandatory prohibitions table (including no-fake-completion and no-dangerous-actions-without-approval), mandatory review questions before each step, and a priority order for every technical decision.

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

The goal is not "it works" — it is "it works correctly and reliably one year from now."

---

## Universality

Code Discipline is **language-agnostic**. The six principles apply equally to Python, TypeScript, Go, Rust, Java, C#, C++, and any other language. See [`EXAMPLES.md`](EXAMPLES.md) for real-world before/after examples across multiple languages.

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
3. Paste the contents of [`SKILL.md`](./skills/code-discipline/SKILL.md) into the skill editor
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
  https://raw.githubusercontent.com/eliranpv11/code-discipline-skills/main/skills/code-discipline/SKILL.md
```

---

### Option F — CLAUDE.md paste (zero-install, works everywhere)

The `CLAUDE.md` file contains the complete methodology without YAML frontmatter — ready to paste directly into any project.

1. Copy the contents of [`CLAUDE.md`](./CLAUDE.md)
2. Paste into your project's `CLAUDE.md` file (merge with existing content if needed)

This approach requires no installation and works in any environment that reads `CLAUDE.md`.

---

### Option G — Cursor IDE

A committed Cursor project rule (`.cursor/rules/code-discipline.mdc`) applies the methodology automatically when you open this repository in Cursor (`alwaysApply: true`).

To use the same rule in another project, see [`CURSOR.md`](CURSOR.md) for full setup, troubleshooting, and how the Cursor rule relates to the Claude Code plugin.

---

### Option H — Codex CLI

A Codex-compatible skill is bundled at [`skills/code-discipline`](skills/code-discipline), including [`agents/openai.yaml`](skills/code-discipline/agents/openai.yaml) for Codex UI metadata and invocation policy.

Install it into the Codex skills directory:

```bash
mkdir -p ~/.codex/skills
cp -R skills/code-discipline ~/.codex/skills/
```

Restart Codex, then invoke the skill explicitly with `$code-discipline` or let Codex load it implicitly when the task fits. See [`CODEX.md`](CODEX.md) for full setup, configuration, and troubleshooting.

---

## How to Use

Once installed, Code Discipline applies automatically. The six principles surface assumptions before coding, verify against the actual codebase, keep changes within scope, respect existing contracts, and define verifiable success criteria for every task.

You can also invoke it explicitly:

> "Apply code-discipline to this task."
> "Use the code-discipline principles for this review."

---

## Verify Installation

Start a Claude Code session and apply the skill to a real task:

```
Apply code-discipline to refactor the function in src/utils.js
```

The response should surface assumptions before writing any code, present options when multiple interpretations exist, and reference the principles or the priority order when relevant — rather than jumping directly into implementation. If it does, the skill is loaded and active.

---

## Updating to a New Version

When a new release is published, update via Claude Code's built-in plugin manager:

1. In Claude Code, run `/plugins`
2. Switch to the **Installed** tab
3. Select **`code-discipline @ code-discipline`** (press Enter to open details)
4. Choose **"Update now"**
5. Close Claude Code completely and reopen — discovery runs at startup

The new version is active across all your sessions.

### Verify the update

Run `/plugins` again, select the plugin, and check the **Version** field — it should match the [latest release](https://github.com/eliranpv11/code-discipline-skills/releases).

### Fallback — force a clean reinstall

If `Update now` does not pick up the new version (rare, e.g. when the local cache is wedged), do a manual reset:

**Windows (PowerShell):**

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.claude\plugins\cache\code-discipline"
Remove-Item -Recurse -Force "$env:USERPROFILE\.claude\plugins\marketplaces\eliranpv11-code-discipline-skills*"
```

**macOS / Linux:**

```bash
rm -rf ~/.claude/plugins/cache/code-discipline
rm -rf ~/.claude/plugins/marketplaces/eliranpv11-code-discipline-skills*
```

Then in Claude Code:

```
/plugin marketplace add eliranpv11/code-discipline-skills
/plugin install code-discipline@code-discipline
```

Close Claude Code completely and reopen.

### Cursor IDE updates

The `.cursor/rules/code-discipline.mdc` file is committed to your project — pull the latest to get the new version:

```bash
git pull
```

If you copied the rule into another project manually, repeat the copy step from [Option G](#option-g--cursor-ide).

### Codex CLI updates

If you installed by copy:

```bash
rm -rf ~/.codex/skills/code-discipline
cp -R skills/code-discipline ~/.codex/skills/
```

If you installed by symlink, the update happens automatically when you `git pull` this repository.

Restart Codex to pick up the new skill body.

---

## Compatibility

- ✅ Claude Code (CLI)
- ✅ Claude.ai (Web & Desktop)
- ✅ Cursor IDE
- ✅ Codex CLI
- ✅ Any agent supporting the Agent Skills open standard

---

## File Structure

```
code-discipline-skills/
├── CLAUDE.md                              ← Copy into any project's CLAUDE.md
├── README.md                              ← This file
├── CURSOR.md                              ← Cursor IDE usage and setup
├── CODEX.md                               ← Codex CLI usage and setup
├── EXAMPLES.md                            ← Real before/after examples
├── LICENSE                                ← MIT
├── .claude-plugin/
│   ├── marketplace.json                   ← Marketplace listing
│   └── plugin.json                        ← Plugin manifest
├── skills/
│   └── code-discipline/
│       ├── SKILL.md                       ← The skill itself
│       └── agents/
│           └── openai.yaml                ← Codex CLI metadata
└── .cursor/
    └── rules/
        └── code-discipline.mdc            ← Cursor IDE rule (alwaysApply: true)
```

---

## Tradeoff

This methodology biases toward **correctness and simplicity over speed and cleverness**. For throwaway scripts and one-off prototypes, apply judgment — not every change needs the full rigor.

The goal is reducing costly mistakes on production work, not slowing down trivial tasks.

---

## License

MIT — see [LICENSE](LICENSE)
