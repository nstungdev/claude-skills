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

Write a PLAN.md — a technical blueprint that answers **what** gets built at the component level, **how** components connect, and **which technical decisions are final** (plus what's explicitly off-limits). It does not answer why or who — that's already in `SPEC.md`. Link to it, don't repeat it.

**Written for two readers at once:**
- **The coder** reviews it to see what Claude intends to build before any code is written — it's a checkpoint, not a formality.
- **Claude** (this session or a later one) uses the Components table as the input for breaking the work into concrete tasks. Each row must stand on its own well enough to become a task later — vague rows make that step harder, not easier.

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
- Tech choices or constraints already stated
- Anything already ruled out or marked as technically out of scope

Only ask about what is **genuinely missing**: the component breakdown, the key tech decisions, and any hard constraints. Ask everything in a single message.

If a decision isn't final yet, don't write it down as one — send it back to `SPEC.md`'s Open Questions instead.

### Step 2 — Generate PLAN.md

Read `references/plan-template.md` and fill it in, using the same `{feature-name}` slug as the feature's `SPEC.md`.

Rules:
- One page max, no prose — tables and short bullets only
- Components: one row per unit of work, with the action (create / modify / delete) and any key constraint
- Data Flow: explicit enough that dependencies between components are obvious
- Tech Decisions: final choices only, one-line rationale — never list options that weren't picked
- Constraints and Out of Scope: hard rules and technical exclusions, not soft preferences

### Step 3 — Confirm and save

Show the full generated PLAN.md to the user. Ask if anything needs adjusting. Once confirmed, save as `specs/{feature-name}/PLAN.md` — the same feature folder as its `SPEC.md`.

---

## Mode: update

`PLAN.md` is a frozen blueprint, not a progress tracker — it records what was decided, not what's done. Once tasks are underway, only touch it if the technical design itself changes.

### When to update

- A component gets added, split, or removed
- A tech decision gets reversed
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
- **Decisions are final when written** — an unresolved choice belongs in `SPEC.md`'s Open Questions, not here
- **Components are future task units** — write each row specific enough that it could be handed off as a task as-is, not vague enough to need re-interpreting later
- **Frozen once tasks start** — edit only when the design itself changes, never to reflect progress
