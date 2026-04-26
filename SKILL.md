---
name: code-discipline
description: >
  A complete discipline for production-grade software development — combining
  structured thinking, surgical changes, truth-first reporting, and systematic
  problem resolution. Use when writing, reviewing, refactoring, or architecting
  any code that must remain correct and maintainable well past initial delivery.
license: MIT
---

# Code Discipline

A complete working method for building production systems that must last.
Not demos. Not experiments. Not code that works until the next change.

**Tradeoff:** This discipline biases toward correctness and long-term reliability over speed.
For throwaway scripts or quick experiments, apply judgment about which rules apply.
For anything production-bound — apply everything.

---

## Priority Order

Every decision is evaluated in this order:

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

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

Before writing any code, present all four:
1. **Working assumptions** — what are you assuming to be true?
2. **Constraints and risks** — what could go wrong?
3. **Multiple valid solutions?** — if yes, stop and present options; do not choose independently
4. **Dependencies and blast radius** — what else is affected by this change?

If information is missing: stop and ask. Do not proceed without answers.

---

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

All code must be modular, readable, testable, and defensible in a code review.
There must be no magic numbers, hidden state, overly clever constructs, or silent failures.

Ask: *"Would a senior engineer say this is overcomplicated?"* If yes, simplify.

---

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

**Required steps for every change:**

BEFORE — Read the file completely. Map dependencies and blast radius.
Identify affected tests. State assumptions explicitly.
Check if a suitable solution already exists in the project.

DURING — Change only what's needed. No unrelated refactoring.
Preserve working behavior. No duplication.

AFTER — Run affected tests. Run full test suite. Verify zero regressions.
Verify tests assert real-world behavior, not just mock matches.
Document in changelog with facts, not narrative.

BEFORE COMMIT — Get explicit approval. Show exactly what's being committed. No hidden files.

The test: *Every changed line should trace directly to the request.*

---

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Testing principles:
- Every change must include tests, or a clear explanation of why testing is not appropriate.
- Tests must verify real behavior — not just match mocks.
- A passing test suite does not mean the code works; it means the tests *think* it works.
- If uncertain about an API, library, or behavior — state certainty level explicitly.

Strong success criteria let you work independently.
Weak criteria ("make it work") require constant clarification.

---

## 5. Truth First

**Report what is real, not what is hoped.**

- Never document something as "done" that isn't done.
- Never claim "production ready" without evidence.
- If something is broken — say **broken**.
- If something is partial — say **partial**.
- If something is stubbed — say **stubbed**.

Every claim requires `file:line` + quoted code.
"It should work" is not evidence.
"Tests pass" requires showing the actual test output.

---

## 6. Systematic, Not Point-Fix

**Understand the system before touching it.**

- Before fixing X, understand what X depends on and what depends on X.
- If fixing X reveals more issues — document all of them; don't silently expand scope.
- Better to delay a fix than deploy a band-aid.
- No quick fixes. No temporary settings without an expiry mechanism.

---

## Mandatory Prohibitions

| Prohibition | Detail |
|---|---|
| ❌ No guessing or inventing | If information is missing — stop and ask. Never fill gaps independently. |
| ❌ No unrequested initiatives | No "we could also" implemented. Any addition requires explicit approval. |
| ❌ No broad refactors | Change only what was explicitly requested. No "while I'm here" modifications. |
| ❌ No band-aids | No temporary values without expiry. No hardcoded test values in production. No silent error swallowing. |
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

## Documentation — Single Source of Truth

- Every topic gets exactly one canonical document.
- No duplicates across folders.
- No contradictions between documents.
- Every canonical document declares: Version, Last Updated, Status, Canonical Location.
- Status documents reflect current reality — not aspirations.
- Planning documents are clearly marked as plans, not status.

---

## Before Any Archival or Deletion Decision

No document, file, or code is archived or deleted without:

1. Deep reading (not a metadata scan)
2. Unique content explicitly identified
3. Answer to: *"If this disappeared tomorrow, what would be lost forever?"*
4. If unique content exists: extract to an active location **before** archiving the source.

Metadata alone (size, date, title) is never sufficient for archival decisions.

---

## Handling Mistakes

When a mistake is found:

1. Acknowledge immediately — no defensiveness.
2. Document root cause.
3. Fix systemically, not just the symptom.
4. Propose how to prevent recurrence.
5. Never hide or minimize the issue.

Being wrong is acceptable. Hiding it is not.

---

## Final Rule

> Reliability and stability are the goal — not speed, not impression.
>
> It is better to stop and ask than to proceed on assumption.
>
> It is better to deliver less, done right, than more, done wrong.
>
> It is better to say "I don't know" than to invent an answer.
