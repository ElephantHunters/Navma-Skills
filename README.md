<p align="center">
  <img src="navma-skills-banner.png" width="220" alt="NavMa Skills">
</p>

# NavMa Skills

A personal library of Claude workflow skills built for builders who orchestrate AI tools — not write code.

These skills are packaged as native Claude Skills — folders containing a `SKILL.md` manifest, ready to upload directly. Once installed, they activate automatically based on Claude's judgment of task relevance — no re-explaining, no re-teaching, no slash command required for most of them.

---

## What's in this library

| Skill | Trigger | What it does |
|---|---|---|
| `build` | `/build` | 4-phase structured build — Research → Plan → Track → Implement. Never writes code before Phase 4. |
| `adversarial-code-review` | `/adversarial-code-review` or `/code_critique` | Two-mode code review. Claude attacks its own output, or provides the exact prompt for a second AI to do so. |
| `really-honest` | `/really-honest` | Forces Claude to re-examine its previous response. What was glossed over? What assumption could be wrong? |
| `harness-first` | `/harness-first` | Scaffolds the test harness before writing any feature logic. Keeps multi-session builds verifiable. |
| `context-loop` | `/context-loop` | Session continuity via AGENTS.md. Prevents re-explaining project context every session. Works in Chat Mode and Project Mode. |
| `silent-fail-check` | `/silent-fail-check` | Manual on-demand file verification. Catches empty PDFs, corrupt DOCX, blank XLSX that "succeeded". |
| `jargon-jail-break` | Auto — paste any technical text | Translates developer jargon into plain English. Feynman Technique + 4 ground rules. Built for non-CS professionals. |

---

## How to install a skill

### Option 1: Skills upload (recommended)

This is the native install path and the one these skills are built for.

1. Download the skill folder you want (e.g. `build/`) and zip it — the skill folder itself must be the root of the zip, not the repo, and not a folder containing the skill folder.

   Correct zip contents:

       build.zip
         build/
           SKILL.md

2. In Claude, go to **Settings > Capabilities** and make sure **Code execution** is turned on. Skills upload requires this.
3. Upload the `.zip` file under the Skills section.
4. Claude reads the `SKILL.md` frontmatter (`name`, `description`) automatically — no further setup needed.
5. Test it by starting a task that matches the skill's description and confirming it triggers.

To install the full stack, repeat for all seven skill folders: `build`, `adversarial-code-review`, `really-honest`, `harness-first`, `context-loop`, `silent-fail-check`, `jargon-jail-break`.

### Option 2: Project Knowledge (fallback)

If you can't enable code execution on your account, you can still use these skills manually:

1. Open the skill's `SKILL.md` file in this repo (inside its matching folder).
2. Copy its contents.
3. Paste into a Claude Project's **Project Knowledge** (Files) section.
4. Claude will read it as reference context, though it won't have the automatic triggering or folder-based discovery that native Skills upload provides.

This fallback works everywhere but loses the "Claude notices this is relevant and loads it on its own" behavior — you're relying on the project instructions to point Claude at the right file instead.

---

## How to use skills once installed

Each skill has its own trigger phrase. Type it in any conversation where the skill is installed:

```
/build — I want to build a WhatsApp notification bot
/adversarial-code-review
/really-honest
/harness-first
/context-loop
/silent-fail-check
jargon-jail-break — paste any block of technical text and ask "what does this mean?"
```

For `silent-fail-check` — type `/silent-fail-check` after any file has been generated to run verification on demand.

---

## Skills detail

### build
Structured 4-phase build protocol. Prevents jumping straight to implementation.

- **Phase 1 — Research:** Best approach, libraries, known pitfalls. No code.
- **Phase 2 — Plan:** Step-by-step build plan. Each phase = one logical chunk of work (optional: one git branch).
- **Phase 3 — Track:** Checkbox checklist of all deliverables.
- **Phase 4 — Implement:** One phase at a time. Stop and show output after each.

Claude waits for your approval between every phase.

---

### adversarial-code-review
Two-mode review skill. The goal is to break the code, not validate it.

- **Mode A (in-chat):** Claude reviews its own code adversarially — logic errors, edge cases, production assumptions, security gaps, efficiency problems. Output ranked CRITICAL / HIGH / MEDIUM / LOW.
- **Mode B (second AI):** Claude provides the exact prompt to paste into ChatGPT or Gemini. Different models catch different bugs.

Josh Pigford (the inspiration for this skill) said this catches 3–5 bugs every single time.

---

### really-honest
Second-pass scrutiny. Claude's first answer is optimistic. This forces a real second look.

- What did I gloss over?
- What assumption could be wrong for your specific situation?
- What's the weakest part of what I just said?
- What would I change if reviewing someone else's answer?

---

### harness-first
Scaffold the test structure before writing any features. Keeps multi-session builds from falling apart.

- Creates `harness.py` and `logger.py` before any feature logic
- Every component is independently testable from the moment it's written
- Harness status is captured in AGENTS.md at session end — next session picks up exactly where you left off

---

### context-loop
Session continuity system using AGENTS.md as Claude's persistent memory.

Claude has zero memory between sessions. This skill fixes that.

**Chat Mode** (standalone conversations):
- Start: paste AGENTS.md → Claude reads, confirms, begins
- End: type `/learnings` → Claude writes structured update → paste into AGENTS.md

**Project Mode** (inside a Claude Project):
- Start: AGENTS.md already in Project Knowledge → loads automatically, no paste needed
- End: type `/learnings` → update AGENTS.md → re-upload to Project Knowledge

One paste at the start, one copy at the end. Zero re-explanation, ever.

---

### silent-fail-check
Manual on-demand file verification. Type `/silent-fail-check` after any file generation to run the checks.

`returncode 0` and "Done" can lie. This skill catches:
- Empty files that appear to have generated successfully
- PDFs with no extractable text (image-only, no real content)
- Corrupt DOCX, blank XLSX, broken exports

Only runs when you type `/silent-fail-check` — it never fires automatically.

---

### jargon-jail-break
Translates technical jargon into plain English, for non-CS professionals working in technical environments.

Built on the **Feynman Technique** — if it can't be explained to a 12-year-old, the explanation isn't done yet.

Triggers automatically when you paste developer docs, error messages, architecture notes, or any block of text dense with technical terms. No slash command needed — the skill recognises jargon and activates.

Every response follows 4 non-negotiable ground rules:

- **Use Analogies** — every term gets anchored to cricket, restaurants, office life, or postal systems. Never to another tech concept.
- **Avoid Jargon** — if explaining a term requires a second technical term, that term gets explained first. Build from the ground up.
- **Visualize Concepts** — text diagrams, before/after comparisons, and step-by-step walkthroughs make abstract ideas tangible.
- **Focus on the "Why"** — every explanation connects to a cost, risk, decision, or next step. No definition stops at "what it is."

Closes every response with a **"What This Means for You"** section — the action or decision the technical information points to.

Banned phrases: "simply", "just", "obviously", "as you may know."

---

## Attribution

The core workflow skills (`build`, `adversarial-code-review`, `really-honest`) were inspired by **Josh Pigford's** AI development workflow, shared by **Peter Yang** on YouTube:

> *"The Exact AI Skills This Solo Founder Uses"* — [@PeterYangYT](https://www.youtube.com/@PeterYangYT)

Josh's original skills were extracted from the video, adapted, renamed, and packaged into installable Claude Skills by Naveen.

Skills built independently by Naveen:
- `context-loop` — built from scratch to solve Claude's session memory gap
- `harness-first` — personal coding protocol for multi-session AI-assisted builds
- `silent-fail-check` — born from a real ReportLab silent failure incident
- `jargon-jail-break` — built to decode developer jargon for non-CS professionals; grounded in the Feynman Technique

---

## About

**NavMa Skills** is a personal toolkit built by **Naveen** — a Product Operations Engineer working at the intersection of AI tools, workflow design, and product thinking.

I don't come from a CS background. I build with AI the way a conductor runs an orchestra — I don't play every instrument, I make sure they all play together.

These skills were born out of a simple frustration: every time I started a new Claude session, I had to re-explain everything from scratch. Every build lacked structure. Every technical output was written for developers, not operators.

So I started building skills that fix those gaps — one workflow problem at a time.

Some were extracted from founder workflows shared publicly. Some were built from scratch after hitting a real wall. All of them are designed for people who work *around* technology, not inside it.

If you're a PM, ops lead, founder, or anyone building with AI without a CS degree — these are for you.
