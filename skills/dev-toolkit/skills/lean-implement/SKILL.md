---
name: lean-implement
description: >
  Executes the tasks in a feature's TASKS.md, wave by wave, flipping status as work completes. Use this skill whenever:
  - The user wants to start implementing tasks ("let's implement", "start building", "work on wave 1", "run the tasks")
  - The user mentions TASKS.md and wants Claude to actually write the code, not just plan it
  Never run without TASKS.md — if it doesn't exist yet, tell the user to run lean-task first.
  Always asks execution mode (sequential/parallel) and run scope (all waves / one wave at a time) at the start of every run, before doing any work — never assume from a prior run.
  A wave's tasks are only marked done once the project builds (and tests, if any) cleanly.
---

# Lean Implement

Execute the tasks in a feature's `TASKS.md`, wave by wave, writing the actual code and keeping status current. It does not decide what to build — that was already decided in `PLAN.md`/`TASKS.md`. It only executes.

---

## Modes

### Step 0 — Check current state

Check whether `specs/{feature-name}/TASKS.md` exists.
- No `TASKS.md` → stop. Tell the user to run `lean-task` first.
- `TASKS.md` exists → proceed to Step 1.

### Step 1 — Ask execution mode and run scope

Ask both questions in a single message, every run — don't reuse a prior run's answers:

1. **Execution mode**: sequential or parallel within a wave?
   - Recommend **sequential** — simpler, no risk of two tasks editing the same area at once.
2. **Run scope**: all waves in one pass, or one wave then stop for review?
   - Recommend **one wave, then stop for review** — matches reviewing the work in checkpoints rather than all at once.

### Step 2 — Identify the next wave to run

Read `TASKS.md`. Find the first wave that still has any `todo` tasks.
- No `todo` tasks anywhere → everything is done. Report progress and stop; there's nothing to implement.

### Step 3 — Execute the wave

- **Sequential**: work through the wave's `todo` tasks one at a time — mark a task `doing` when starting it, implement exactly the change described, then move to the next. Don't mark `done` yet — that happens after Step 4.
- **Parallel**: dispatch all of the wave's `todo` tasks at once, one subagent per task, each implementing its own bounded diff. Wait for all to finish before Step 4.

If a task's real scope turns out bigger than what `TASKS.md` describes, stop and flag it rather than quietly expanding it — don't silently redesign mid-implementation.

### Step 4 — Build check before marking anything done

Once every task in the wave has been implemented, run the project's build (and test suite, if one exists) before marking any of them `done`.

- **Clean** → mark all completed tasks in the wave `done`, update the Progress line in `TASKS.md`.
- **Not clean** → do not mark anything done. Fix the break (attribute it to the specific task if you can), re-run the build check, and only mark done once it's clean. If the fix isn't straightforward, leave the task as `doing`, add a Note describing the failure, and stop — don't guess past a real error.

### Step 5 — Report, then continue or stop

- **One-wave scope** → report what was done and the current Progress line, then stop. Wait for the user before running the next wave.
- **All-waves scope** → report the wave's outcome, then repeat from Step 2 for the next wave. Keep going until every task is done — but a wave that won't build clean stops the run regardless of scope, and is reported the same way as one-wave scope.

---

## Principles

- **Executes, doesn't decide** — what gets built was already decided in `PLAN.md`/`TASKS.md`; this skill writes code, it doesn't redesign
- **Ask fresh, every run** — execution mode and run scope are asked at the start of every invocation, never carried over
- **A clean build gates `done`** — no task is marked done until its wave builds (and tests, if present) without errors
- **Never push through a broken build** — even in all-waves mode, a failing build stops the run there; report and wait rather than continuing to the next wave
- **Status only, no redesign** — updates `TASKS.md`'s Status/Progress/Notes; doesn't rewrite `PLAN.md`, `SPEC.md`, or `CONTEXT.md`
- **Bounded tasks stay bounded** — if a task's real scope exceeds its one-line description, stop and flag it instead of expanding it quietly
