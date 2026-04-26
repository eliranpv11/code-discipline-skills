# Examples

Real-world before/after examples showing how each of the four principles applies in practice. The examples span four different languages — **Python**, **TypeScript**, **Go**, and **Rust** — to demonstrate that Code Discipline is fundamentally **language-agnostic**. The same failure modes and the same corrections apply identically across stacks.

For each principle: the bad version is what an unconstrained assistant will typically produce; the good version is what Code Discipline produces.

---

## 1. Think Before Coding — Silent Assumptions (Python)

**User Request:** "Add user authentication to the API."

### ❌ Without Code Discipline

The assistant immediately produces ~150 lines of code:

```python
import jwt
import bcrypt
from datetime import datetime, timedelta
from functools import wraps
from flask import request, jsonify, Flask

app = Flask(__name__)

SECRET_KEY = "change-me-in-prod"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30
REFRESH_TOKEN_EXPIRE_DAYS = 7

def create_access_token(data: dict) -> str:
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire, "type": "access"})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def create_refresh_token(data: dict) -> str:
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(days=REFRESH_TOKEN_EXPIRE_DAYS)
    to_encode.update({"exp": expire, "type": "refresh"})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def verify_token(token: str) -> dict | None:
    try:
        return jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
    except jwt.PyJWTError:
        return None

def require_auth(roles: list[str] | None = None):
    def decorator(f):
        @wraps(f)
        def wrapped(*args, **kwargs):
            auth = request.headers.get("Authorization", "")
            if not auth.startswith("Bearer "):
                return jsonify({"error": "Missing token"}), 401
            payload = verify_token(auth.removeprefix("Bearer "))
            if not payload:
                return jsonify({"error": "Invalid token"}), 401
            if roles and payload.get("role") not in roles:
                return jsonify({"error": "Forbidden"}), 403
            request.user = payload
            return f(*args, **kwargs)
        return wrapped
    return decorator

# ... another 80 lines for OAuth, password reset, refresh-token rotation,
#     rate limiting, audit logging, and a User model nobody asked for.
```

**Problems with this output:**

| Problem | Why It Matters |
|---|---|
| Assumed JWT instead of sessions | If the app is server-rendered with cookies, sessions may be the right choice. Wrong assumption = wrong architecture. |
| Assumed Flask | The project may be FastAPI, Django, Starlette, or none of those. |
| Hardcoded `SECRET_KEY` | Production secrets must come from env vars or a secret store. Hardcoding is a security defect. |
| Assumed token lifetimes | 30 minutes / 7 days are guesses. Wrong values harm UX or security. |
| Implemented role-based access control | Was never requested. Adds complexity and attack surface. |
| Implemented refresh tokens | Was never requested. |
| Assumed which routes need protection | Every route? Some routes? The assistant did not ask. |

### ✅ With Code Discipline

```
Before writing any code, I need to surface four things.

1. Working assumptions — flag any wrong:
   - I will assume stateless token-based auth (JWT) unless you confirm sessions
   - I will assume secrets come from environment variables, not source code
   - I will assume only the routes you specify need protection

2. Constraints and risks:
   - JWT secrets must never be committed. Needs a configured env var.
   - Token expiry directly impacts UX vs security tradeoff. Wrong value
     either logs users out constantly or extends a stolen token's lifetime.
   - If the existing routes use cookies, switching to JWT changes the
     client contract — front-end work required.

3. Multiple valid approaches — I will not choose for you:
   a. JWT (stateless, scales horizontally, harder to revoke)
   b. Server-side sessions (stateful, simpler revocation, requires a session store)
   c. API keys (machine-to-machine only, not for user login)

4. Blast radius:
   - Existing route handlers will need a decorator or middleware
   - Tests for those routes will need auth headers added
   - Frontend code that calls the API needs to send credentials

Three questions before I proceed:
- Which auth model fits the system: JWT, sessions, or API keys?
- Which routes need protection — all of them, or specific ones?
- Where do you want secrets stored — env vars, AWS Secrets Manager, Vault?

I will not start coding until you answer these.
```

**Why this is correct:** Every assumption is named. Every option is presented. The blast radius is mapped. The work cannot start with the wrong premise because the premise is verified first.

---

## 2. Simplicity First — Overengineering (TypeScript)

**User Request:** "Add a function that formats a `Date` as `YYYY-MM-DD`."

### ❌ Without Code Discipline

```typescript
import { DateTime, Settings } from "luxon";

export enum DateFormat {
  ISO = "yyyy-MM-dd",
  US = "MM/dd/yyyy",
  EU = "dd.MM.yyyy",
  RFC3339 = "yyyy-MM-dd'T'HH:mm:ssZZ",
  UNIX = "UNIX",
  HUMAN = "HUMAN",
}

export interface DateFormatterOptions {
  timezone?: string;
  locale?: string;
  fallback?: string;
  cacheSize?: number;
}

export type DateInput = Date | string | number | DateTime | null | undefined;

export class DateFormatter {
  private readonly timezone: string;
  private readonly locale: string;
  private readonly fallback: string;
  private readonly cache: Map<string, string>;
  private readonly cacheSize: number;

  constructor(options: DateFormatterOptions = {}) {
    this.timezone = options.timezone ?? "UTC";
    this.locale = options.locale ?? "en-US";
    this.fallback = options.fallback ?? "";
    this.cacheSize = options.cacheSize ?? 1000;
    this.cache = new Map();
    Settings.defaultZone = this.timezone;
  }

  format(input: DateInput, fmt: DateFormat = DateFormat.ISO): string {
    if (input == null) return this.fallback;
    const key = `${String(input)}:${fmt}`;
    const cached = this.cache.get(key);
    if (cached !== undefined) return cached;
    const dt = this.toDateTime(input);
    if (!dt.isValid) return this.fallback;
    const result = fmt === DateFormat.UNIX
      ? String(dt.toMillis())
      : fmt === DateFormat.HUMAN
      ? dt.toRelative({ locale: this.locale }) ?? this.fallback
      : dt.toFormat(fmt);
    this.evictIfFull();
    this.cache.set(key, result);
    return result;
  }

  private toDateTime(input: DateInput): DateTime {
    if (input instanceof DateTime) return input;
    if (input instanceof Date) return DateTime.fromJSDate(input);
    if (typeof input === "string") return DateTime.fromISO(input);
    if (typeof input === "number") return DateTime.fromMillis(input);
    return DateTime.invalid("unsupported input");
  }

  private evictIfFull(): void {
    if (this.cache.size < this.cacheSize) return;
    const firstKey = this.cache.keys().next().value;
    if (firstKey !== undefined) this.cache.delete(firstKey);
  }
}
```

**Problems with this output:**

| Problem | Why It Matters |
|---|---|
| Asked for one format, got six | Five of those formats will never be called. Dead code from day one. |
| Pulled in `luxon` as a dependency | The standard library can do `YYYY-MM-DD` in one line. Adds bundle size and supply-chain risk. |
| Built a class with caching | A pure function has no state to cache. The cache is a fictional optimization. |
| Configurable timezone, locale, fallback | None of these were requested. Each option is a behavior the test suite must cover. |
| LRU-style eviction | Solving an imaginary problem (cache size) created by a previous imaginary problem (caching). |
| ~50 lines for a 1-line task | Every reader has to absorb 50 lines to understand a one-line behavior. |

### ✅ With Code Discipline

```typescript
export function formatDate(date: Date): string {
  return date.toISOString().slice(0, 10);
}
```

**Why this is correct:** It does exactly what was asked, in the standard library, in one line. If a future request adds another format or timezone handling, that complexity gets added **then** — when an actual requirement justifies it. Not now, on speculation.

The senior-engineer test:
> *"Would a senior engineer reviewing this PR say it is overcomplicated?"*

For the bad version: yes, immediately. For the good version: no.

---

## 3. Surgical Changes — The "While I'm Here" Refactor (Go)

**User Request:** "Fix the off-by-one error in `Sum` at `math.go:14`. The last element is being skipped."

### Existing Code

```go
// math.go
package math

// Sum returns the sum of all values in xs.
func Sum(xs []int) int {
    total := 0
    for i := 0; i < len(xs)-1; i++ {  // line 14 — bug here
        total += xs[i]
    }
    return total
}
```

### ❌ Without Code Discipline

The assistant "fixes" the bug **and** does a drive-by refactor:

```go
// math.go
package math

// Sum returns the sum of all integer values in the input slice.
// Returns 0 for an empty or nil slice.
//
// Example:
//
//   Sum([]int{1, 2, 3}) // 6
func Sum(values []int) int {
    if len(values) == 0 {
        return 0
    }
    sum := 0
    for _, v := range values {
        sum += v
    }
    return sum
}
```

**Problems with this output:**

| Problem | Why It Matters |
|---|---|
| Renamed parameter `xs` to `values` | Every caller and test that grepped for the old name now finds nothing. |
| Renamed local `total` to `sum` | Same problem — silent rename of an internal name. |
| Switched index loop to `range` | Different mechanic. Slightly different performance characteristics. Not the bug. |
| Added an empty-slice guard | Was never broken. The original loop already returns 0 on an empty slice. New code, new test surface, new untested branch. |
| Added a doc comment with example | Was not requested. Comments rot. |
| Diff is unreviewable | "What was the actual fix?" cannot be answered from the diff. |

### ✅ With Code Discipline

```go
// math.go
package math

// Sum returns the sum of all values in xs.
func Sum(xs []int) int {
    total := 0
    for i := 0; i < len(xs); i++ {  // off-by-one fix: was len(xs)-1
        total += xs[i]
    }
    return total
}
```

The diff is one character — `-1` removed from the loop bound. The reviewer can see the fix and verify it in five seconds.

The assistant's report:

```
Fixed: off-by-one at math.go:14. Loop bound was `len(xs)-1`,
which skipped the last element. Changed to `len(xs)`.

Other observations (NOT touched):
- Variable names `xs` and `total` could be more descriptive.
- A doc comment with examples might help.
- An empty-slice short-circuit could be added.

Each of these is a separate request. Let me know if you want any of them
done as a follow-up — I will not bundle them with this fix.
```

**Why this is correct:** Every changed line traces directly to the bug report. Adjacent observations are surfaced for the user to decide on, not silently bundled into the same diff.

---

## 4. Goal-Driven Execution — Weak Success Criteria (Rust)

**User Request:** "The test suite is failing. Make the tests pass."

### The Failing Test

```rust
// tests/refund.rs
use payments::{process_refund, RefundError};

#[test]
fn refund_exceeding_balance_is_rejected() {
    let account = Account::new("user-1", 50);  // balance: $50
    let result = process_refund(&account, 100); // refund: $100
    assert!(matches!(result, Err(RefundError::InsufficientBalance)));
}
```

### The Production Code

```rust
// src/refund.rs
pub fn process_refund(account: &Account, amount: u64) -> Result<(), RefundError> {
    // No balance check — this is the bug.
    account.debit(amount);
    Ok(())
}
```

### ❌ Without Code Discipline — Outcome A: Delete the Test

```rust
// tests/refund.rs — file emptied
// "The test was checking edge case behavior that isn't implemented yet.
//  Removed it so the suite passes cleanly."
```

The suite is now green. The bug is still in production. The next refund that exceeds balance will silently overdraw the account.

### ❌ Without Code Discipline — Outcome B: Mock Away the Failure

```rust
#[test]
fn refund_exceeding_balance_is_rejected() {
    let mock = MockProcessor::new()
        .with_response(Err(RefundError::InsufficientBalance));
    let result = mock.process_refund(100);
    assert!(matches!(result, Err(RefundError::InsufficientBalance)));
}
```

The test passes. The test asserts that **the mock returns what the mock was configured to return**. It tests nothing about the real `process_refund` function. The bug is still in production.

**Why both outcomes fail:** The user said "make the tests pass." The assistant treated that as the goal. But the **real** goal is "the system behaves correctly." Passing tests is supposed to be evidence of correct behavior. Deleting tests or mocking around them produces the evidence without the correctness.

### ✅ With Code Discipline

```
Before touching anything, clarifying success criteria.

"Make the tests pass" has three possible interpretations:
  A. Fix the production code so the assertion is genuinely true.   ✅ Will do
  B. Delete or skip the failing test.                              ❌ Will not do
  C. Mock the failure away.                                        ❌ Will not do

Diagnosis:
- Failing test: tests/refund.rs::refund_exceeding_balance_is_rejected
- It expects: process_refund returns Err(InsufficientBalance) when
  the refund amount exceeds the account balance.
- Current code at src/refund.rs:3 does not check the balance — it
  always returns Ok(()). The test correctly fails because the bug
  is real.

Plan:
1. Add a balance check at src/refund.rs:3 before debiting.
   Verify: the failing test passes.
2. Run the full test suite.
   Verify: no other test regressed.
3. Confirm the test was previously asserting real production behavior
   (not a mock), so passing it represents a real fix.

Proposed change:

  pub fn process_refund(account: &Account, amount: u64) -> Result<(), RefundError> {
      if amount > account.balance() {
          return Err(RefundError::InsufficientBalance);
      }
      account.debit(amount);
      Ok(())
  }

Shall I proceed with this fix?
```

**Why this is correct:** The success criterion is explicit and verifiable — *the test assertion holds because the production code now satisfies it*. Not because the test was deleted, not because the call was mocked. The fix is grounded in the real failure, traced to the real line of code, and verified against the real test.

---

## Anti-Pattern Cheat Sheet

| Principle | Anti-Pattern | Code Discipline Response |
|---|---|---|
| Think Before Coding | Silently picks one interpretation, writes 200 lines | Names all assumptions, presents options, asks before coding |
| Simplicity First | Adds caching, abstractions, configurability nobody asked for | Writes the smallest code that satisfies the request |
| Surgical Changes | Renames, reformats, refactors "while there" | Changes only the lines the request requires; surfaces other observations as separate items |
| Goal-Driven Execution | Deletes failing tests or mocks them away | Reproduces the failure against real code, fixes the real cause, verifies with the real test |

---

## Why This Pattern Repeats

The "without" examples are not obviously wrong. Each one looks reasonable in isolation:

- The Python example uses real authentication patterns.
- The TypeScript example uses real software-engineering principles (composition, configurability, caching).
- The Go example follows real Go idioms (`range`, doc comments, guard clauses).
- The Rust example uses real test infrastructure.

The problem is **timing**: each one adds correctness or flexibility **before it is needed**. That added complexity:

- Makes the code harder to read
- Adds untested branches and dependencies
- Makes the diff impossible to review
- Hides the real change inside a sea of unrelated edits

The "with" versions look smaller because they **are** smaller. They solve the specific problem in front of them and leave everything else untouched. When the next requirement arrives, that requirement gets its own focused change.

> Good code is code that solves today's problem simply, not tomorrow's problem prematurely.
