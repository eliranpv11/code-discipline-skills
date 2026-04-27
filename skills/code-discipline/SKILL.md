---
name: code-discipline
description: Coding methodology for production-grade software development. Enforces structured thinking before coding, verifying reality, simplicity, surgical changes, contract awareness, and verifiable success criteria. Use when writing, reviewing, or refactoring code that needs to remain reliable over time, including production code, shared libraries, or code others will maintain. Triggers include "fix a bug", "add a feature", "refactor", "review code", "clean up", or explicit "apply code-discipline". Do NOT use for throwaway scripts, one-off prototypes, or quick demos.
license: MIT
metadata:
  version: 1.4.1
---

# Code Discipline

A coding methodology for production systems that must last.
Not a communication standard. Not an honesty protocol. Purely: how to write code well.

**Tradeoff:** This discipline biases toward correctness and simplicity over speed and cleverness.
For throwaway scripts, apply judgment. For production code — apply everything.

---

## Priority Order

Every technical decision is evaluated in this order:

1. **Stability**
2. **Reliability**
3. **Readability**
4. **Maintainability**
5. **Scalability**
6. **Performance**
7. **Elegance**

The goal is not "it works" — it is "it works correctly and reliably one year from now."

---

## 1. Think Before Coding

**Don't assume. Surface tradeoffs. Map blast radius.**

Before writing any code, present all four:
1. **Working assumptions** — what are you assuming to be true?
2. **Constraints and risks** — what could go wrong?
3. **Multiple valid solutions?** — if yes, stop and present options; do not choose independently
4. **Dependencies and blast radius** — what else is affected by this change?

Rules:
- If uncertain — ask, don't assume and proceed
- If multiple interpretations exist — present them, don't pick silently
- If a simpler approach exists — say so and push back
- If information is missing — stop and ask; do not proceed without answers

---

## 2. Verify Reality Before Acting

**Don't code from memory. The codebase is the source of truth.**

Before writing or changing code:
- Read the actual files involved. Don't work from a remembered shape.
- Don't invent APIs, function names, type names, field names, environment variables, configuration keys, or imports.
- Don't assume a pattern, helper, or utility exists unless you verified it in the repository.
- Don't assume a third-party library behaves a certain way without checking its documentation or signature.
- If you cannot find evidence in the codebase, say so explicitly: *"I couldn't find this in the code"* — and stop. Do not fill the gap from memory.

**The test:** *Every function call, import, type, and field in your code must trace to a verified source — an actual file, an actual signature, or documentation you read.*

---

## 3. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked
- No abstractions for single-use code
- No "flexibility" or "configurability" that wasn't requested
- No error handling for impossible scenarios
- If you write 200 lines and it could be 50, rewrite it

All code must be modular, readable, testable, and defensible in a code review.
There must be no magic numbers, hidden state, overly clever constructs, or silent failures.

Ask: *"Would a senior engineer say this is overcomplicated?"* If yes, simplify.

---

## 4. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting
- Don't refactor things that aren't broken
- Match existing style, even if you'd do it differently
- If you notice unrelated dead code, mention it — don't delete it
- Remove only imports/variables/functions that YOUR changes made unused

**Required steps:**

BEFORE — Read the file completely. Map dependencies and blast radius.
Identify affected tests. Check if a suitable solution already exists in the project.

DURING — Change only what's needed. No unrelated refactoring.
Preserve working behavior. No duplication.

AFTER — Run affected tests. Run full test suite. Verify zero regressions.
Verify tests assert real-world behavior, not just mock matches.
Document in changelog with facts, not narrative.

BEFORE COMMIT — Get explicit approval. Show exactly what's being committed. No hidden files.

**The test:** *Every changed line should trace directly to the request.*

---

## 5. Respect Existing Contracts

**Public surfaces are commitments. Treat them as such.**

Before changing behavior, identify the contracts your change touches. Contracts include:
- Function and method signatures (parameters, return types, thrown errors)
- API request and response shapes
- Database schemas and column types
- Event names and payloads
- CLI flags and command names
- Configuration keys and environment variables
- Error codes and error formats
- File formats your code reads or writes

Rules:
- Don't change a contract silently. A silent rename, removal, or reshape is a breaking change disguised as cleanup.
- If a contract must change, surface the impact: who consumes it, what breaks, what migration looks like.
- Prefer **additive** changes (new field, new endpoint, new flag) over **breaking** changes (rename, remove, restructure).
- When in doubt about who depends on a surface, search the codebase and ask before changing.

**The test:** *If your change crosses a system boundary, you must name the boundary and the consumers before changing it.*

---

## 6. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Testing principles:
- Every change must include tests, or a clear explanation of why not
- Tests must verify real behavior — not just match mocks
- A passing suite means the tests *think* it works — verify they test the right thing
- State certainty level explicitly when uncertain about an API or behavior

Strong success criteria let you work independently.
Weak criteria ("make it work") require constant clarification.

---

## Mandatory Prohibitions

| Prohibition | Detail |
|---|---|
| ❌ No guessing or inventing | If information is missing — stop and ask. Never fill gaps independently. |
| ❌ No unrequested features | No "we could also" implemented. Any addition requires explicit approval. |
| ❌ No broad refactors | Change only what was requested. No "while I'm here" modifications. |
| ❌ No band-aids | No temporary values without expiry. No hardcoded test values in production. No silent error swallowing. |
| ❌ No fake completion | Never claim "done" without verification. If something was not verified, say so explicitly: *"Implemented, but not verified because..."* |
| ❌ No dangerous actions without approval | Explicit approval required before destructive actions: deleting files, dropping schemas, force-pushing, removing tests, disabling checks, large rewrites, or modifying authentication or authorization logic. |
| ❌ No committing without approval | The user must say "commit now" before `git commit`, "push now" before `git push`, "deploy now" before deployment. |

---

## Mandatory Review Questions

Before each step, answer all four:

1. Is this the most professional and simplest solution?
2. Does this change preserve what already works?
3. Is this a reasonable production-grade solution?
4. Would this pass code review at a top-tier engineering organization?

**Only if all four answers are positive — proceed.**

---

**These guidelines are working if:** changes are smaller and more focused, no unrequested features appear in diffs, every task has a verifiable definition of done before work begins, and contracts are preserved unless an explicit migration is agreed upon.
