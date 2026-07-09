---
name: build
description: >
  Use this skill whenever the user starts a new build, project, or feature request.
  Triggers when the user types /build, says "build me", "create", "make", "I want
  to build", or describes something they want to ship. This skill prevents the
  class of problems where Claude jumps straight to implementation without
  understanding the problem, choosing the right approach, or planning phases.
  Always apply this skill before writing any code. Research and planning first —
  implementation last.
---

# Build

A structured 4-phase approach to every build. Forces research and planning
before a single line of code is written. Prevents building the wrong thing fast.

The 4 phases must happen in order. Claude does not move to the next phase
until the user approves the current one.

---

## The 4 Phases

### Phase 1 — Research

Before planning anything, understand the problem and the landscape.

Claude must answer:
- What is the best approach for this specific problem?
- What libraries, APIs, or tools are most appropriate?
- What are the known pitfalls or edge cases for this type of build?
- Are there simpler alternatives to what the user described?

Output: a short research summary (5–10 lines max). No code yet.

Wait for the user's approval before moving to Phase 2.

---

### Phase 2 — Plan

Turn the research into a concrete, step-by-step build plan.

Claude must produce:
- A numbered list of build phases (each phase = one logical chunk of work)
- What each phase produces (a file, a function, a working endpoint, etc.)
- Any dependencies between phases (Phase 3 requires Phase 2 to be complete)
- Estimated complexity per phase (simple / moderate / complex)

Output: a structured plan. No code yet.

If you use git, each phase in the plan can map to a separate branch:
`feature/phase-1-scaffold`, `feature/phase-2-core-logic`, etc.
This is optional — skip it if you don't use version control.

Wait for the user's approval before moving to Phase 3.

---

### Phase 3 — Track

Before implementing, create a tracking checklist from the plan.

Claude must produce:
- A checkbox list of every deliverable across all phases
- Current status markers: [ ] not started, [~] in progress, [x] done
- Any open questions or blockers that need resolving before implementation

Output: a tracking checklist. No code yet.

This checklist is the source of truth for the build. Update it at the
start of every subsequent session by pasting it into AGENTS.md.

Wait for the user's approval before moving to Phase 4.

---

### Phase 4 — Implement

Now write the code — one phase at a time.

Rules for implementation:
- Build Phase 1 first. Stop. Show the user the output.
- Only move to Phase 2 after the user confirms Phase 1 works.
- Never build multiple phases in one response.
- Each phase should be independently testable before the next begins.
- Add comments to all non-obvious code so it's readable in future sessions.
- Use harness-first approach if applicable (see harness-first skill).

After each phase: update the tracking checklist.

---

## Claude's Behaviour When /build Triggers

1. Acknowledge the build request in one sentence
2. Move immediately to Phase 1 — Research
3. Do NOT ask clarifying questions before starting — start researching,
   then ask if anything is genuinely ambiguous
4. Present each phase clearly with a header: "## Phase 1 — Research"
5. Wait for approval between phases — never auto-proceed
6. If the user skips approval and asks to continue, confirm the skip explicitly

---

## Trigger Phrases

| Trigger | Claude action |
|---|---|
| /build | Start Phase 1 immediately |
| "build me [X]" | Start Phase 1 immediately |
| "I want to build [X]" | Start Phase 1 immediately |
| "create [X]" | Start Phase 1 immediately |
| "make [X] for me" | Start Phase 1 immediately |
| "let's build [X]" | Start Phase 1 immediately |

---

## Rules

- Never skip to Phase 4 without completing Phases 1–3
- Never write code in Phase 1, 2, or 3
- Never build multiple phases in a single response
- Always wait for the user's approval between phases
- If the user pushes for speed, acknowledge it but still complete Phase 1
  and Phase 2 before touching code — the plan takes minutes, the bugs
  from skipping it take hours
- Each build phase can map to a separate git branch — optional if you don't use version control

---

## Origin

Extracted from Josh Pigford's workflow (via Peter Yang's YouTube video).
Josh runs Research → Plan → Track → Implement on every build, broken into
separate git branches and pull requests. This discipline is what allows him
to manage 5 AI products simultaneously without losing quality.
