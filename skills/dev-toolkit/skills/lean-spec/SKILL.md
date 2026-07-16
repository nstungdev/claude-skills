---
name: lean-spec
description: >
  Creates a lean, reviewable SPEC.md for a feature or project. Use this skill whenever:
  - The user wants to build or design something new ("I want to build X", "let's add a feature for Y")
  - The user asks to write a spec, requirement, or feature description
  - The user mentions SPEC.md or asks whether a feature is properly scoped
  Never fill in unknown details — always surface gaps and wait for answers before proceeding.
  The output must fit on one page. If it doesn't, cut it.
---

# Lean Spec

Write a SPEC.md that is short enough to review in 60 seconds and honest about what isn't known yet.

The spec answers: **what** is being built, **why** it matters, and **who** it's for.
It does not answer: how to build it, what tech to use, or how long it will take — that belongs in `PLAN.md`.

---

## Step 1 — Extract, don't interrogate

If a `CONTEXT.md` exists in the project root, read it first — it may already answer the domain and user questions below, so you don't have to ask them.

Before asking anything, extract what's already available from the conversation (and from `CONTEXT.md`, if read):
- The feature or thing being built
- The user it serves
- The problem it solves
- Any constraints or non-goals mentioned

Only ask about what is **genuinely missing**. Ask everything in a single message — never one question at a time.

Flag gaps explicitly with `[?]` rather than guessing. A spec with honest gaps is better than a spec with confident assumptions.

Check whether `specs/{feature-name}/SPEC.md` already exists. If it does, confirm with the user whether this is a revision of that spec or a genuinely new one before continuing.

---

## Step 2 — Generate SPEC.md

Read `references/spec-template.md` and fill it in.

Rules:
- Each section: 2–4 lines max
- No bullet lists longer than 5 items
- No technical implementation details
- Every `[?]` must be a real unanswered question — remove it once answered
- If the spec exceeds one page, cut until it fits

---

## Step 3 — Surface gaps before confirming

Show the full generated SPEC.md to the user. If any `[?]` remain, call them out explicitly:

```
Before this spec is ready to plan against, these gaps need answers:
- [?] ...
- [?] ...
```

Do not consider the spec ready for planning until all `[?]` are resolved or explicitly marked as "won't decide now". Ask if anything else needs adjusting.

---

## Step 4 — Save

Once the user confirms, save as `specs/{feature-name}/SPEC.md`.

---

## Principles

- **Gaps are not failures** — a `[?]` is honest; a confident assumption is a hidden risk
- **One page is a hard limit** — if you can't fit it, the feature isn't scoped yet
- **No implementation details** — the moment you write "we'll use X to do Y", stop; that's `PLAN.md`
- **This spec is for humans first** — Claude will read it too, but write it so a person can review it in 60 seconds
- **Frozen once planning starts** — once a `PLAN.md` exists against this spec, treat scope changes as a deliberate revision, not a silent edit