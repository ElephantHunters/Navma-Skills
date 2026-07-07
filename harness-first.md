---
name: harness-first
description: >
  Use this skill whenever Naveen is starting a new coding project, script, or
  automation. Triggers when Naveen types /harness-first, says "start a new
  project", "scaffold this", "set up the structure", or begins any build where
  code will be written across multiple sessions. This skill prevents the class
  of problems where builds lack a testing structure, making it impossible to
  verify that individual components work before wiring them together.
  Always apply at the start of Phase 4 (Implement) in the /build skill.
  Harness-first is how Naveen's code stays reliable across multi-session builds.
---

# Harness-First

A code scaffolding protocol that sets up the testing and verification
structure before writing any feature logic. The harness is built first —
then the features are built inside it.

Builds without a harness are fragile. Bugs hide until they compound.
Harness-first means every component is independently verifiable from
the moment it's written.

---

## What a Harness Is

A harness is a lightweight wrapper that:
- Lets you run any individual component in isolation
- Prints clear pass/fail output for each test
- Makes it obvious when something breaks before you wire components together
- Requires zero external testing frameworks — just Python or Node.js built-ins

A harness is NOT a full test suite. It is NOT pytest or Jest (unless the
project already uses them). It is the minimum structure needed to verify
that each piece works before moving to the next.

---

## The Harness-First Protocol

### Step 1 — Scaffold the project structure first

Before any feature code:

```
project/
├── main.py          # entry point
├── harness.py       # test runner — built first
├── logger.py        # logs every run automatically
├── .env             # secrets and config (never committed)
├── requirements.txt # dependencies
└── README.md        # what this does and how to run it
```

### Step 2 — Build the harness before building features

`harness.py` contains:
- A list of test cases (what input, what expected output)
- A runner that calls each component with the test input
- Clear PASS / FAIL output for each test
- A summary at the end: "3/4 tests passed"

Example harness structure:

```python
# harness.py
import sys
from main import process_input  # import the function being tested

tests = [
    {"input": "hello", "expected": "HELLO", "label": "basic uppercase"},
    {"input": "",       "expected": "",      "label": "empty string"},
    {"input": None,     "expected": None,    "label": "null input"},
]

passed = 0
for test in tests:
    result = process_input(test["input"])
    status = "PASS" if result == test["expected"] else "FAIL"
    if status == "PASS":
        passed += 1
    print(f"[{status}] {test['label']}")
    if status == "FAIL":
        print(f"       Expected: {test['expected']}")
        print(f"       Got:      {result}")

print(f"\n{passed}/{len(tests)} tests passed")
sys.exit(0 if passed == len(tests) else 1)
```

### Step 3 — Build features one at a time, run harness after each

For every new function or component:
1. Write the function
2. Add a test case to harness.py
3. Run the harness
4. Only move to the next function when this one passes

### Step 4 — logger.py runs automatically on every session

`logger.py` logs each run with a timestamp, inputs used, and outcome.
This creates a session history that feeds directly into /learnings at
session end.

Example log entry:
```
[2026-06-14 20:45:12] RUN harness.py
  Tests: 3/4 passed
  FAIL: null input — got TypeError instead of None
  Duration: 0.3s
```

---

## Rules for Claude When Harness-First Triggers

1. Always create `harness.py` before writing any feature logic
2. Always create `logger.py` before running anything
3. Always scaffold the full folder structure in Phase 1 of implementation
4. Never write two components without testing the first
5. After each component is built, show Naveen the harness output —
   do not proceed until it shows PASS
6. When a test fails, fix it before adding new tests
7. Add comments to all non-obvious code — this is Naveen's /comment-all
   preference and applies to all code generated in this session

---

## AGENTS.md Integration

At session end (/learnings), the harness state is captured:

```
CURRENT HARNESS STATUS
  Tests: X/Y passing
  Failing: [list any failing tests]
  Last run: [timestamp]
```

At session start, Claude reads this from AGENTS.md and knows exactly
where the build stands without any re-explanation.

---

## When Harness-First Applies

| Situation | Apply harness-first? |
|---|---|
| New Python script | Yes |
| New Node.js project | Yes |
| API integration | Yes — test each endpoint independently |
| Data pipeline | Yes — test each transformation step |
| Quick one-off script (< 20 lines) | Optional |
| Existing project with tests already | No — use existing test structure |

---

## Rules

- Harness before features — always
- One component at a time — always
- PASS before proceeding — always
- Logger runs automatically — always
- Never skip the scaffold to "save time" — the scaffold is what
  saves time when things break in Session 3

---

## Origin

Developed by Naveen as a personal coding protocol for multi-session
AI-assisted builds. The core insight: Claude has no memory between sessions,
so the harness and logger create a verifiable record of what works.
At session start, Naveen pastes AGENTS.md which includes harness status —
Claude knows instantly what's passing, what's failing, and where to start.

Harness-first is what makes the /build + /context-loop combination
actually reliable across sessions.
