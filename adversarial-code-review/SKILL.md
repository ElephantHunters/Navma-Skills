---
name: adversarial-code-review
description: >
  Use this skill whenever Naveen has completed a build and wants the code
  reviewed before shipping. Triggers when Naveen types /adversarial-code-review,
  /code-review, or /code_critique, says "review this code", "check this before
  I ship", "what did you miss", or pastes code and asks for a critique.
  This skill prevents the class of problems where code that looks complete
  has subtle bugs, edge cases, or assumptions that break in production.
  Always apply after any build session before going live. Claude reviews its
  own code adversarially — or provides the exact prompt for a second AI to do so.
---

# Adversarial Code Review

A two-mode code review skill. Claude attacks its own output rather than
defending it. When a second AI is available, provides the exact prompt to
extract maximum bug-finding value from a different model.

The goal is NOT to validate the code. The goal is to break it.

---

## Two Modes

### Mode A — In-Chat Review (Claude reviews its own code)

When Naveen types /code_critique or /adversarial-code-review in this chat,
Claude reviews the most recently produced code as if it were reviewing
someone else's work — adversarially, not helpfully.

Claude must answer these questions for every review:

**Logic errors**
- Does the core logic actually do what it claims?
- Are there off-by-one errors, wrong conditionals, or inverted checks?
- Are there race conditions if anything runs concurrently?

**Edge cases**
- What happens with empty input?
- What happens with null / None / undefined values?
- What happens at the boundaries (0, max int, empty string, empty list)?
- What happens with unexpected data types?

**Production assumptions**
- What does this code assume about the environment that might not be true?
- Does it assume network availability? File permissions? Specific OS?
- Does it assume the API always responds correctly?

**Security gaps**
- Are there injection risks (SQL, command, path)?
- Are credentials or secrets handled correctly?
- Is user input ever trusted without validation?

**Efficiency problems**
- Are there N+1 query patterns?
- Are there obvious performance bottlenecks?
- Is anything being computed repeatedly that could be cached?

Output format — rank every issue by severity:

```
CRITICAL — will break in production
[list issues]

HIGH — likely to cause problems under real conditions
[list issues]

MEDIUM — edge cases that should be handled
[list issues]

LOW — improvements worth making if time allows
[list issues]

VERDICT
[one sentence: ship it / fix criticals first / needs significant rework]
```

---

### Mode B — Second AI Review (Claude provides the prompt)

When Naveen wants to run the code through ChatGPT, Gemini, or another model,
Claude provides this exact prompt to paste into the second AI:

```
Here is code written by Claude. Your job is NOT to be helpful.
Your job is adversarial.

Find every bug, every edge case, every assumption that will break
in production. Do not explain what the code does. Do not validate
what works. Only attack what could fail.

Rank every issue by severity:
CRITICAL / HIGH / MEDIUM / LOW

Be brutal. The goal is to ship reliable code, not to feel good
about the current state.

[PASTE CODE HERE]
```

After the second AI responds, Naveen brings the findings back to Claude
with: "GPT flagged these — fix them." Claude then addresses each finding.

---

## When to Use Which Mode

| Situation | Mode |
|---|---|
| Quick check mid-session | Mode A — in-chat |
| Before going live on anything important | Mode B — second AI |
| After a complex build with many moving parts | Both modes |
| Naveen has ChatGPT open | Mode B — different blind spots |

---

## Claude's Behaviour When Triggered

1. Do NOT validate or praise the code before critiquing it
2. Start immediately with the most severe issues found
3. If no critical issues are found, say so explicitly — do not pad the review
4. Never say "looks good overall" — say "no critical issues found"
5. After the review, ask: "Do you want me to fix any of these?"

---

## Rules

- The goal is to find bugs, not to reassure
- Never lead with praise — lead with problems
- If the code is genuinely solid, say so concisely — don't invent issues
- Always rank by severity — CRITICAL issues are fixed before shipping
- In Mode B, always give Naveen the exact prompt — don't paraphrase it
- After fixes are applied, run the review again on the updated code

---

## Origin

Extracted from Josh Pigford's workflow (via Peter Yang's YouTube video).
Josh builds with Claude Opus then runs the output through GPT-4 adversarially.
He noted this catches 3–5 bugs every single time — bugs that the original
model missed because it was too close to its own output to critique it
objectively. Different models have different blind spots; using two is the fix.
