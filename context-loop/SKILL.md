---
name: context-loop
description: >
  Use this skill to manage session continuity across Claude conversations using
  AGENTS.md as a persistent project memory file. Triggers whenever Naveen starts
  a new session and pastes AGENTS.md, types /context-loop, says "let's pick up
  where we left off", "here's my AGENTS.md", "briefing", or asks Claude to
  capture session learnings at the end of a conversation. Also triggers at session
  end when Naveen types /learnings. This skill prevents the class of problems
  where Claude loses all project context between sessions and Naveen has to
  re-explain everything from scratch. Always apply this skill at the start AND
  end of every project session — it is the save-and-restore system for all builds.
---

# Context Loop

A session continuity protocol that uses AGENTS.md as Claude's persistent memory
across conversations. Claude has no memory between sessions — this skill is the fix.

The loop has two moments: **session start** (restore) and **session end** (save).
Both are required. Skipping the end means the next session starts blind.

Naveen works in two modes. The skill behaves slightly differently in each.

---

## Mode Detection — Chat vs Project

Before applying the loop, Claude should detect which mode is active:

**Chat Mode** — a standalone conversation, not inside a Claude Project.
Naveen typically converts interesting chats into Projects once they reach
a meaningful conclusion or produce something worth continuing.

**Project Mode** — inside a Claude Project. Project Knowledge is available.
AGENTS.md lives in Project Knowledge, not on Naveen's desktop.
Static files (skills, preferences, shortcodes) are already loaded automatically.

If unsure which mode is active, check whether Project Knowledge is present
in context. If it is — Project Mode. If not — Chat Mode.

---

## Chat Mode

Used for standalone conversations. Naveen pastes AGENTS.md manually each session.

```
SESSION START
  └── Naveen pastes AGENTS.md into chat
  └── Claude reads it, confirms understanding, begins work

... session happens ...

SESSION END
  └── Naveen types: /learnings
  └── Claude writes structured update (see format below)
  └── Naveen copies the output
  └── Naveen pastes it into AGENTS.md (replaces old content)
  └── Saves file on device for next session

CHAT GETS INTERESTING / REACHES MEANINGFUL CONCLUSION
  └── Naveen converts chat to a Claude Project
  └── Creates AGENTS.md from the conversation so far
  └── Uploads AGENTS.md to Project Knowledge
  └── Switches to Project Mode from here
```

**Chat Mode rules:**
- If Naveen forgets to paste AGENTS.md, prompt: "Do you have an AGENTS.md
  for this project? Paste it and I'll pick up exactly where we left off."
- If no AGENTS.md exists yet, offer to create one from the current conversation.

---

## Project Mode

Used once a chat has been converted into a Claude Project.
AGENTS.md lives in Project Knowledge — no manual paste needed at session start.

```
SESSION START
  └── AGENTS.md is already in Project Knowledge — Claude reads it automatically
  └── Claude confirms understanding, begins work
  └── No paste required from Naveen

... session happens ...

SESSION END
  └── Naveen types: /learnings
  └── Claude writes structured update (see format below)
  └── Naveen copies the output
  └── Naveen updates AGENTS.md and re-uploads to Project Knowledge
      (replaces the old version)

NEXT SESSION
  └── Updated AGENTS.md is already in Project Knowledge
  └── Loop repeats — no paste, no friction
```

**Project Mode rules:**
- Do NOT ask Naveen to paste AGENTS.md — it is already in Project Knowledge
- Do NOT ask Naveen to re-explain static context (skills, preferences,
  shortcodes) — those are already loaded via Project Knowledge
- The only thing that changes session to session is AGENTS.md content —
  focus confirmation paragraph on what changed since last session
- After /learnings, remind Naveen: "Update AGENTS.md in Project Knowledge
  before closing — replace the old version."

**What Project Knowledge handles automatically (no loop needed):**
- Skill files (silent-fail-check, context-loop, etc.)
- Shortcode library and preferences
- Harness-first protocol
- Any static reference documents

**What the loop still handles in Project Mode:**
- Current build state — what was built this session
- What broke and why — session-specific failures
- What to avoid — rules discovered this session
- Next step — where to pick up next time

---

## Session Start — Claude's Behaviour (both modes)

When context-loop triggers at session start:

1. Read AGENTS.md completely before responding
2. Confirm understanding in ONE short paragraph — what the project is,
   where it was left, what the next step is
3. Call out any blockers or unresolved issues from last session
4. Do NOT ask Naveen to re-explain anything already in the file
5. Begin work immediately — no preamble, no "great, let's get started"

**Example confirmation:**
"Got it — ElevenLabs Hindi audio app. Last session we got TTS output
working but hit asyncio conflicts with Flask. Today's target is the retry
logic. Avoiding threading.Thread — using tenacity instead. Ready."

---

## Session End — /learnings Format (both modes)

When Naveen types /learnings, output EXACTLY this structure.
Keep the entire update under 200 words. Be specific — no vague summaries.

```
## Session update — [DATE]

WHAT WE BUILT
[1-3 lines. Specific. What files, what features, what commands.]

WHAT BROKE AND WHY
[1-3 lines. Exact error or failure. Root cause if known.]

WHAT TO AVOID NEXT TIME
[Bullet list. Concrete rules. "Don't use X" not "be careful with X".]

WHAT WORKED WELL
[1-2 lines. Approaches, libraries, or patterns worth repeating.]

CURRENT STATUS
[One line. Where things stand right now.]

NEXT STEP
[One line. The exact first thing to do next session.]
```

After outputting the update, append one of these depending on mode:

**Chat Mode:** "Copy this and paste into your AGENTS.md before closing."
**Project Mode:** "Update AGENTS.md in Project Knowledge before closing —
replace the old version with this."

---

## AGENTS.md Structure (same in both modes)

```
PROJECT: [name]
STACK: [languages, APIs, tools]
WHAT WE'VE BUILT SO FAR: [brief description of working components]
KNOWN BLOCKERS: [anything unresolved]
WHAT TO AVOID: [concrete rules from past failures]
CURRENT STATUS: [one line]
NEXT STEP: [one line]

--- SESSION HISTORY ---
[Paste /learnings outputs here, newest first]
```

---

## Shortcode Triggers

| Trigger | Moment | Claude action |
|---|---|---|
| Paste AGENTS.md | Session start (Chat) | Read, confirm, begin |
| AGENTS.md in Project Knowledge | Session start (Project) | Read automatically, confirm, begin |
| /context-loop | Session start | Detect mode, act accordingly |
| /learnings | Session end | Output structured update + mode-specific reminder |
| "pick up where we left off" | Session start | Detect mode, ask for AGENTS.md if Chat |
| "briefing" | Session start | Detect mode, act accordingly |
| "convert this to a project" | Mid-chat | Offer to create AGENTS.md from current conversation |

---

## Conversion Moment — Chat to Project

When Naveen says the chat has been converted to a project, or asks to
convert it, Claude should:

1. Offer to generate a fresh AGENTS.md from the current conversation
2. Format it using the standard structure above
3. Tell Naveen: "Upload this to Project Knowledge — it becomes your
   permanent briefing file for this project."
4. From the next session, switch to Project Mode behaviour.

---

## Rules

- Never ask Naveen to re-explain context already in AGENTS.md
- Never skip /learnings — it is the save point
- /learnings output must always follow the exact format above
- Keep each session update under 200 words — brevity is the point
- In Project Mode, never ask for a manual paste — trust Project Knowledge
- In Chat Mode, always prompt for AGENTS.md if it hasn't been pasted
- Always end /learnings with the correct mode-specific reminder

---

## Origin

Born from the insight that Claude's lack of session memory is the biggest
friction point for solo builders doing multi-session projects. AGENTS.md +
/learnings turns a stateless AI into a persistent project collaborator.

Chat Mode: one paste at the start, one copy at the end.
Project Mode: one re-upload at the end. No paste ever.

The benefit in both cases: zero re-explanation, ever.
