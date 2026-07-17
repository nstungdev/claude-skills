---
name: lean-plan
description: >
  Creates a lean, actionable PLAN.md for a feature that already has a SPEC.md. Use this skill whenever:
  - The user wants to plan how to build something already spec'd ("let's plan this out", "how should we build X")
  - The user asks to write a plan, component breakdown, or technical blueprint for a feature
  - The user mentions PLAN.md or asks what Claude intends to build before writing code
  Never plan a feature that has no SPEC.md — if one doesn't exist yet, ask the user to create it first (or suggest lean-spec).
  One page, no prose, no options — only final decisions.
---

# Lean Plan

Write a PLAN.md that a reviewer can approve or reject in 30–60 seconds, answering four questions: **what** gets built, **how** (briefly), **what it affects**, and **what it explicitly won't do**. It does not answer why or who — that's already in `SPEC.md`. Link to it, don't repeat it.

**Written for a reviewing supervisor, not a line-by-line coder.** The user delegates implementation to Claude and reviews checkpoints rather than reading code directly — so even though `PLAN.md` is more technical than `SPEC.md`, it must stay a fast approve/reject read. Full technical breakdown (step-by-step approach, data flow, rationale per decision) does NOT belong here — that level of detail is for `lean-task` once implementation actually starts. `PLAN.md`'s "How" section is a 2–4 line summary of approach, not the full breakdown.

The Components table doubles as input for later task breakdown — each row must stand on its own well enough to become a task later, but the plan itself stays short.

---

## Modes

### Step 0 — Check current state

Check whether `specs/{feature-name}/SPEC.md` exists before doing anything else.

- No `SPEC.md` → stop. A plan built on an unscoped feature just encodes assumptions as fact. Tell the user to run `lean-spec` first.
- `SPEC.md` exists, no `PLAN.md` → **`init`**
- `PLAN.md` exists → **`update`**

## Mode: init

### Step 1 — Extract, don't interrogate

Read the feature's `SPEC.md` — it already carries the domain and user context; don't re-derive it here.

Extract what's already available from the conversation:
- Components or modules already mentioned
- The high-level approach, if already discussed
- Existing systems/features/data likely to be touched
- Anything already ruled out or marked as technically out of scope
- Hard constraints already stated

Only ask about what is **genuinely missing**: the component breakdown, the approach, impact, and any hard constraints. Ask everything in a single message.

If a decision isn't final yet, don't write it down as one — send it back to `SPEC.md`'s Open Questions instead.

### Step 2 — Generate PLAN.md

Read `references/plan-template.md` and fill it in, using the same `{feature-name}` slug as the feature's `SPEC.md`.

Rules:
- One page max, tables and short bullets only — the four review sections must read in under a minute
- **What** (Components): one row per unit of work, with the action (create / modify / delete) and any key detail
- **How**: 2–4 lines, plain language, high-level approach only — no options, no per-decision rationale, no step-by-step. If it's growing past 4 lines, that detail belongs in `lean-task`, not here
- **Impact**: existing systems, files, features, or data this touches — this is what lets a reviewer judge blast radius without reading code
- **Won't Do**: explicit technical exclusions, not soft preferences
- **Constraints**: hard rules only

### Step 3 — Confirm and save

Show the full generated PLAN.md to the user. Ask if anything needs adjusting. Once confirmed, save as `specs/{feature-name}/PLAN.md` — the same feature folder as its `SPEC.md`.

---

## Mode: update

`PLAN.md` is a frozen blueprint, not a progress tracker — it records what was decided, not what's done. Once tasks are underway, only touch it if the technical design itself changes.

### When to update

- A component gets added, split, or removed
- The approach changes
- The blast radius (impact) turns out to be different than planned
- A new hard constraint is discovered mid-build

### How to update

1. Read the existing `PLAN.md`
2. Edit only the affected row or section — do not rewrite the whole file
3. Append a row to the Revision Log with the absolute date (`YYYY-MM-DD`, resolved from the current date — never a relative term like "today") and a short description of what changed and why
4. Trim the Revision Log to the 10 most recent entries
5. Update `Last updated` at the top

If nothing about the technical design has actually changed, don't edit — progress alone (a component getting built) is not a reason to touch this file.

---

## Principles

- **A blueprint, not a tracker** — `PLAN.md` records what was decided, not what's done; don't add status columns or checkboxes
- **Built on the spec, not a copy of it** — link to `SPEC.md`, don't restate its problem, users, or scope
- **Written for the reviewer's 60 seconds, not the implementer's hour** — the four review sections (What / How / Impact / Won't Do) are the whole point; anything that only serves implementation detail belongs in `lean-task`
- **"How" is a summary, not a spec** — if it needs step-by-step or per-decision rationale to make sense, that's too much detail for this file
- **Decisions are final when written** — an unresolved choice belongs in `SPEC.md`'s Open Questions, not here
- **Components are future task units** — write each row specific enough that it could be handed off as a task as-is, not vague enough to need re-interpreting later
- **Frozen once tasks start** — edit only when the plan itself changes, never to reflect progress
