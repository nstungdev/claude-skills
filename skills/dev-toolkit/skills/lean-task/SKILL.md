---
name: lean-task
description: >
  Breaks a feature's PLAN.md into a lean, trackable TASKS.md — small, parallelizable units of work. Use this skill whenever:
  - The user wants to break a planned feature into tasks ("break this into tasks", "let's start implementing", "what should we work on first")
  - The user asks to generate, view, or update TASKS.md
  - The user asks what's done, what's left, or wants a progress check on a feature
  Never break down a feature that has no PLAN.md — if one doesn't exist yet, tell the user to run lean-plan first.
  Tasks must be small enough to review as a single diff, and grouped into waves that maximize what can run in parallel.
  This skill only produces the breakdown and updates status — it does not implement tasks itself; that is lean-implement.
---

# Lean Task

Break a feature's `PLAN.md` into `TASKS.md` — small, independently reviewable units of work, grouped into waves so as much as possible can run in parallel. It does not answer what/how/impact/won't-do — that's already in `PLAN.md`. Link to it, don't repeat it.

**The one file in this chain that's meant to change constantly.** `CONTEXT.md`, `SPEC.md`, and `PLAN.md` are frozen once their layer is done — `TASKS.md` is the opposite: its whole job is to track live status. Don't treat status changes as events worth logging; only structural changes (a task added, split, or removed) are.

**Still a fast read for the reviewing supervisor.** The user approved the breakdown in `PLAN.md` already, so this isn't a second design review — but they still skim `TASKS.md` to see the shape of the work and check progress. Keep the top of the file scannable in seconds: how many waves, how many tasks, what's done.

---

## Modes

### Step 0 — Check current state

Check whether `specs/{feature-name}/PLAN.md` exists before doing anything else.

- No `PLAN.md` → stop. A task breakdown built on an unplanned feature just invents structure. Tell the user to run `lean-plan` first.
- `PLAN.md` exists, no `TASKS.md` → **`init`**
- `TASKS.md` exists → **`update`**

## Mode: init

### Step 1 — Extract from PLAN.md

Read the feature's `PLAN.md` — its Components table, Impact, and Constraints are the input. Don't re-derive What/How/Impact/Won't Do here; use them only to judge task sizing and real dependencies.

### Step 2 — Break into tasks

For each Component (or the smallest sub-unit within it), write a task that:
- Is bounded to a single reviewable diff — one file, one function, one schema, one test suite
- Names which `PLAN.md` Component it belongs to

If a task's impact can't be stated in one line, split it further.

### Step 3 — Group into waves, maximizing parallelism

Only put a task in a later wave when it genuinely can't start until another task's concrete output exists (a schema, a function signature, a file) — never because tasks are topically related or touch the same component. Default everything without a real dependency into Wave 1. Fewer waves, more per wave, is the goal.

### Step 4 — Generate TASKS.md

Read `references/tasks-template.md` and fill it in, using the same `{feature-name}` slug as the feature's `PLAN.md`.

### Step 5 — Confirm and save

Show the full generated TASKS.md to the user. This is a shape-check (wave count, task count, sizing), not a deep re-review of the plan. Ask if anything needs splitting, merging, or re-waving. Once confirmed, save as `specs/{feature-name}/TASKS.md` — the same feature folder as its `SPEC.md` and `PLAN.md`.

---

## Mode: update

`TASKS.md` changes constantly. Most edits need no ceremony.

### Edit directly, no Change Log entry

- Flipping a task's status (`todo` → `doing` → `done`)
- Updating the Progress line
- Adding a short blocker note

### Log a Change Log entry

- A task is added, split, or removed
- A task moves to a different wave (a real dependency was discovered or resolved)

### How to update

1. Read the existing `TASKS.md`
2. For status flips — edit the Status cell and the Progress line directly
3. For structural changes — edit the affected rows/waves, then append one line to the Change Log with the absolute date (`YYYY-MM-DD`, resolved from the current date — never a relative term like "today")
4. Trim the Change Log to the 10 most recent entries
5. Update `Last updated` at the top

If `PLAN.md` changes after `TASKS.md` already exists, flag the mismatch and ask whether to regenerate the affected tasks — don't silently drift out of sync.

---

## Principles

- **Small and bounded, not vague** — each task should be describable as a single reviewable diff; if you can't say what it touches in one line, split it further
- **Maximize parallelism, don't force sequence** — only create a wave dependency when a task genuinely needs another task's concrete output
- **A tracker, not a blueprint** — unlike `SPEC.md`/`PLAN.md`, constant change here is expected and fine
- **Structural changes are logged, status changes aren't** — adding/splitting/removing a task is a Change Log entry; finishing one is just an edit
- **Built on the plan, not a copy of it** — link to `PLAN.md`, don't restate its What/How/Impact/Won't Do
- **Reviewed fast, more than once** — a shape-check when created, a progress skim anytime after
- **Not lean-implement** — this skill produces and tracks the breakdown; actually writing the code for a task is a separate step
