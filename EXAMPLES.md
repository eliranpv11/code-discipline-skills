# Examples

Real-world code examples demonstrating the four principles. Each example shows the failure mode and the correct behavior.

---

## 1. Think Before Coding — Silent Assumptions

**User Request:** "Add user authentication to the API"

**❌ Without Code Discipline**

```python
# Claude immediately writes 200+ lines:
import jwt
from datetime import datetime, timedelta
from functools import wraps

SECRET_KEY = "your-secret-key"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def verify_token(token: str):
    # ... 50 more lines of JWT logic

class OAuth2Handler:
    # ... 80 more lines for OAuth support nobody asked for

class SessionManager:
    # ... 60 more lines for sessions nobody asked for
```

**Problems:**
- Assumed JWT (maybe sessions were intended)
- Assumed OAuth support (not requested)
- Assumed token expiry duration
- Assumed which routes need protection
- Hardcoded secret key in source

**✅ With Code Discipline**

```
Before writing any code, I need to clarify four things:

1. **Working assumptions:**
   - I'm assuming stateless auth (JWT tokens), not session-based
   - I'm assuming all API routes need protection unless specified otherwise

2. **Constraints and risks:**
   - JWT secrets must not be hardcoded — needs environment variable
   - Token expiry affects UX — wrong value could log users out constantly

3. **Multiple valid approaches:**
   - **JWT** (stateless, scales horizontally) — my recommendation for APIs
   - **Sessions** (stateful, simpler for browser clients)
   - **API Keys** (for machine-to-machine use cases)

4. **Blast radius:**
   - All existing route handlers will need a decorator or middleware
   - Tests for those routes will need auth headers added

Which auth type fits your use case? And should I protect all routes or specific ones?
```

---

## 2. Simplicity First — Overengineering

**User Request:** "Add a function to format a date as YYYY-MM-DD"

**❌ Without Code Discipline**

```python
from enum import Enum
from typing import Optional
from datetime import datetime, date, timezone
import pytz

class DateFormat(Enum):
    ISO = "YYYY-MM-DD"
    US = "MM/DD/YYYY"
    EU = "DD.MM.YYYY"
    UNIX = "UNIX"

class DateFormatter:
    """
    A flexible, timezone-aware date formatting utility.
    Supports multiple output formats and locale configuration.
    """
    def __init__(self, timezone: str = "UTC", locale: str = "en_US"):
        self.timezone = pytz.timezone(timezone)
        self.locale = locale
        self._format_cache = {}

    def format(
        self,
        dt: Optional[datetime | date | int | str],
        fmt: DateFormat = DateFormat.ISO,
        include_time: bool = False,
        humanize: bool = False,
    ) -> str:
        # ... 80 more lines
```

**Problems:**
- Asked for one format, got a configurable multi-format system
- Asked for a function, got a class with cache and locale support
- Added an enum nobody asked for
- Added timezone awareness nobody asked for
- Requires `pytz` dependency for a one-liner task

**✅ With Code Discipline**

```python
def format_date(dt):
    return dt.strftime("%Y-%m-%d")
```

If more formats are needed later, the function can be extended then. Not now.

---

## 3. Surgical Changes — The "While I'm Here" Refactor

**User Request:** "Fix the off-by-one error in `calculate_total()` at line 47"

**❌ Without Code Discipline**

```python
# Claude "fixes" line 47 AND:
# - Renames variables for "clarity"
# - Adds type hints throughout
# - Adds a docstring
# - Extracts a helper function
# - Reformats spacing
# - Removes a comment it found confusing

def calculate_total(items: list[dict]) -> float:
    """
    Calculate the total price for a list of items.
    
    Args:
        items: List of item dicts with 'price' and 'quantity' keys
    
    Returns:
        Total price as float
    """
    def get_item_total(item: dict) -> float:
        return item["price"] * item["quantity"]
    
    return sum(get_item_total(item) for item in items)
```

**Problems:**
- Changed function signature (breaks callers)
- Renamed variables (breaks grepping for the old names)
- 40 lines changed for a 1-line bug
- Impossible to review — what was the actual fix?

**✅ With Code Discipline**

```python
# Only line 47 changed:
# Before: for i in range(len(items) - 1):
# After:  for i in range(len(items)):

One line changed. Bug: loop stopped one item short — last item was
never processed. Fix: changed range upper bound from len(items) - 1
to len(items) at calculate_total():47.

I also noticed: variable names are unclear and type hints are missing.
I did not touch those — let me know if you'd like a separate cleanup pass.
```

---

## 4. Goal-Driven Execution — Weak Success Criteria

**User Request:** "Make the tests pass"

**❌ Without Code Discipline**

```python
# Claude "makes tests pass" by deleting the failing test:

# test_payment.py - DELETED test_refund_exceeds_balance
# "The test was checking edge case behavior that isn't implemented yet.
#  Removed it so the suite passes cleanly."
```

Or worse:

```python
# Claude mocks away the failure:
@patch("payments.process_refund", return_value={"status": "ok"})
def test_refund_exceeds_balance(mock_refund):
    result = process_refund(user_id=1, amount=99999)
    assert result["status"] == "ok"  # always passes, tests nothing
```

**Problems:**
- "Tests pass" was the goal — achieved by deleting the evidence
- The underlying bug still exists in production
- The mock tests nothing real

**✅ With Code Discipline**

```
Before touching anything, clarifying success criteria:

"Make the tests pass" could mean:
A. Fix the code so the tests pass correctly (real fix)
B. Remove or skip the failing tests (not a fix)
C. Mock away the failure (hides the bug)

I will not do B or C. Proceeding with A.

Failing test: `test_refund_exceeds_balance` at `tests/test_payment.py:34`
It asserts that a refund exceeding the account balance raises `InsufficientFundsError`.

Current behavior at `payments/refund.py:19`: no balance check — refund goes through
regardless of balance.

Fix: add balance check at `payments/refund.py:19` before processing.

Success criteria: `test_refund_exceeds_balance` passes, all other tests still pass,
no new mocks introduced. Shall I proceed?
```
