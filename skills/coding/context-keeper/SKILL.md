---
name: context-keeper
description: >
  Creates and maintains a CONTEXT.md file that captures the essential project context
  for vibe coding sessions — reducing guesswork and wasted tokens. Use this skill whenever:
  - The user wants to initialize a CONTEXT.md for a new project ("setup context", "init context", "create project context")
  - The user has just made code changes and wants to keep CONTEXT.md in sync ("update context", "sync context", "context is outdated")
  - The user starts a new coding session and wants Claude to understand the project before diving in
  - The user mentions CONTEXT.md or asks whether Claude understands their project
  Always use this skill when project context setup or maintenance is involved, even if the user doesn't say "context" explicitly.
---

# Context Keeper

Maintain a `CONTEXT.md` file as the single source of truth for any coding project.

A good `CONTEXT.md` answers three questions:
1. What domain is this project in?
2. Who uses it?
3. Where does it stand right now?

Everything else (business rules, tech stack, coding conventions) belongs in other files. Keep this one lean so it actually gets read.

`CONTEXT.md` is not `CLAUDE.md`. `CLAUDE.md` documents the codebase (architecture, conventions, how to build/test). `CONTEXT.md` documents the *product* (domain, users, current stage). If both exist, keep them separate — don't duplicate content between them.

---

## Modes

### Step 0 — Check current state

Check whether `CONTEXT.md` already exists at the project root before doing anything else.

- If the project has multiple sub-projects (a monorepo), ask the user which directory the `CONTEXT.md` should describe rather than assuming the repo root.
- No file, or user wants to start fresh → **`init`**
- File exists → **`update`**

## Mode: init

### Step 1 — Gather context

Ask all questions in a single message — do not ask one at a time. Extract answers already available in the conversation first; only ask what's genuinely missing.

Questions to cover:

1. **Domain** — What field or industry is this? What key terms does Claude need to know to interpret requests correctly?
2. **Target users** — Who are the primary users? What do they need?
3. **Current status** — What stage is the project at, and what is actively being worked on right now?

### Step 2 — Generate CONTEXT.md

Read `references/context-template.md` and fill it in with the gathered answers.
Write concisely — each section should be 2–4 sentences max.

### Step 3 — Confirm and save

Show the generated file to the user before saving. Ask if anything needs adjusting. Once confirmed, save to the project root as `CONTEXT.md`.

---

## Mode: update

### When to update

Suggest updating `CONTEXT.md` when:
- The project moves to a new stage (e.g. prototype → MVP)
- The domain focus shifts or new key terminology is introduced
- The target users change
- What's actively being worked on changes significantly

### How to update

1. Read the existing `CONTEXT.md`
2. Identify which sections are now outdated. If nothing has actually changed, say so and stop — don't edit just to have done something.
3. Edit only those sections — do not rewrite the whole file
4. Append a row to the Change Log with the absolute date (`YYYY-MM-DD`, resolved from the current date — never a relative term like "today" or "last week") and a short description of what changed
5. Trim the Change Log to the 10 most recent entries, dropping the oldest rows — this file should stay short
6. Update `Last updated` at the top

Keep edits surgical. The file's value comes from being stable and trustworthy — not from being rewritten every session.

---

## Principles

- **Short is correct** — if `CONTEXT.md` is getting long, something is wrong; move detail to `DOMAIN.md` or other dedicated files
- **Domain section sets the vocabulary** — write it so Claude can interpret ambiguous terms correctly without asking
- **Status is the only section that changes often** — the others should be nearly frozen once the project is underway