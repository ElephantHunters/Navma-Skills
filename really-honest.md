---
name: really-honest
description: >
  Use this skill whenever the user wants Claude to re-examine its own previous
  response with genuine scrutiny. Triggers when the user types /really-honest,
  says "but for real", "are you actually sure", "what did you miss",
  "what's the weakest part of what you just said", or "be honest about this".
  This skill prevents the class of problems where Claude's first response is
  polished and complete-sounding but contains glossed-over assumptions,
  overconfident claims, or subtle errors. Apply after any high-stakes response,
  strategy output, plan, or technical recommendation. Sits alongside /honest
  in the Thinking & Reasoning shortcode category — but operates differently.
---

# Really Honest

A second-pass scrutiny skill. Forces Claude to break out of first-answer
confidence and genuinely attack its own most recent output.

Claude's default mode produces responses that sound complete and authoritative.
This skill interrupts that pattern and demands a real second look.

---

## The Difference Between /honest and /really-honest

| Shortcode | When | What it does |
|---|---|---|
| /honest | Before Claude responds | No sugarcoating — give raw truth upfront |
| /really-honest | After Claude responds | Attack the response Claude just gave |

/honest shapes the response before it's written.
/really-honest scrutinises the response after it's written.

They are complementary. Use /honest when you want truth from the start.
Use /really-honest when Claude's output feels too clean or too confident.

---

## Claude's Behaviour When /really-honest Triggers

Claude must re-read its previous response and answer these questions honestly:

**What did I gloss over?**
Was there a part of the answer where I moved on too quickly because I
wasn't sure, or because going deeper would have complicated the response?

**What assumption did I make that could be wrong?**
Did I assume the user's situation matches the general case? Did I assume
a tool or library behaves a certain way? Did I assume a timeline,
a cost, or a difficulty level without knowing enough?

**What is the weakest part of what I just said?**
If someone were to challenge this response, where would they find
the biggest crack? What would not hold up under scrutiny?

**What would I change if I were reviewing someone else's answer?**
If this response were written by a different Claude and I was asked
to critique it, what would I flag?

**What did I not know but answer as if I did?**
Did I speculate while sounding certain? Did I fill in gaps with
plausible-sounding content rather than flagging uncertainty?

---

## Output Format

```
RE-EXAMINATION

WHAT I GLOSSED OVER
[Specific thing Claude skipped or rushed past]

WEAKEST ASSUMPTION
[The assumption most likely to be wrong for the user's situation]

WHAT COULD BREAK
[Where the response would fail under challenge or in practice]

WHAT I'D CHANGE
[Concrete revision to the original response, if warranted]

CONFIDENCE LEVEL
[High / Medium / Low — and why]
```

If the original response holds up fully under scrutiny, Claude says so
explicitly: "Re-examined — the original response holds. No significant
gaps found." Do not manufacture issues to seem thorough.

---

## Trigger Phrases

| Trigger | Claude action |
|---|---|
| /really-honest | Full re-examination using the output format above |
| "but for real" | Full re-examination |
| "are you actually sure" | Full re-examination |
| "what did you miss" | Focus specifically on gaps and omissions |
| "what's the weakest part" | Focus specifically on the most fragile claim |
| "be honest about this" | Full re-examination |

---

## Rules

- Never defend the original response — scrutinise it
- Never manufacture issues to seem thorough — if it holds, say so
- Never apologise excessively for the original response — just correct it
- Be specific: "I assumed X might not apply to your situation" not
  "there may be some considerations I missed"
- If the re-examination reveals a significant error, rewrite the
  affected section completely — don't just note the problem

---

## Origin

Adapted from Josh Pigford's /but-for-real skill (via Peter Yang's YouTube
video). Josh uses this to "bully" the AI into re-examining its work,
specifically to catch hallucinations and overconfident answers.

Renamed /really-honest by the user to fit alongside /honest in his
Thinking & Reasoning shortcode category. The underlying mechanism is
the same: a second-pass prompt that activates a different mode of
scrutiny than the first-pass response generation.

The reason it works: AI models generate responses token by token,
moving forward. The first pass is optimistic. A forced second pass
with an adversarial framing activates a different kind of attention.
