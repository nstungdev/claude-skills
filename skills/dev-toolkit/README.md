# dev-toolkit

A lean chain of five skills that carries a feature from project context to working code, each layer built on the one before it — never re-asking what a prior layer already answered, never bloating a file past what it needs to do its one job.

## Why this exists

Built for a workflow where the human is a reviewing supervisor, not a line-by-line coder: you delegate the work to Claude and approve it at checkpoints. That means every generated file has to serve a fast, honest review — no filler, no re-derived context, no "documentation for documentation's sake." If a file is getting long, that's a signal something is wrong, not a sign of thoroughness.

## The chain

| # | Skill | Produces | Reads | Nature |
|---|-------|----------|-------|--------|
| 1 | `lean-context` | `CONTEXT.md` | — | Frozen, rarely changes (domain / users / status) |
| 2 | `lean-spec` | `specs/{feature}/SPEC.md` | `CONTEXT.md` | Frozen once planning starts |
| 3 | `lean-plan` | `specs/{feature}/PLAN.md` | `SPEC.md` | Frozen once tasks start |
| 4 | `lean-task` | `specs/{feature}/TASKS.md` | `PLAN.md` | Living tracker — status changes freely |
| 5 | `lean-implement` | (code) | `TASKS.md` | Executes — no new doc of its own |

Each skill checks whether the file it depends on exists before doing anything, and refuses to guess at a layer it hasn't been given (e.g. `lean-plan` won't run without a `SPEC.md` — it tells you to create one first).

## How to use

1. **`lean-context`** — once per project. Captures domain, target users, and current status. Linked from `CLAUDE.md` so Claude reads it automatically every session.
2. **`lean-spec`** — once per feature. A one-page `SPEC.md`: problem, users, scope, out of scope, open questions. Written for a human to review in 60 seconds — no implementation details, no guessed answers (gaps are marked `[?]`, not filled in).
3. **`lean-plan`** — once `SPEC.md` exists. A one-page `PLAN.md` answering four review questions: **What** gets built, **How** (a 2–4 line summary, not a full breakdown), **Impact** (what existing systems/files this touches), and **Won't Do** (explicit exclusions). Still a 30–60 second read, even though it's more technical than `SPEC.md`.
4. **`lean-task`** — once `PLAN.md` exists. Breaks the plan's components into small, single-diff-sized tasks in `TASKS.md`, grouped into waves that maximize what can run in parallel. Unlike the files above, this one is a living tracker — status is edited freely as work happens.
5. **`lean-implement`** — once `TASKS.md` exists. Actually writes the code, wave by wave. Asks two questions fresh at the start of every run — sequential or parallel execution, and whether to run all waves or stop after each one for review — and never marks a wave's tasks `done` until the project builds (and tests, if any) cleanly.

Typical flow for a new feature:

```
lean-context (once)
  → lean-spec "add X feature"
    → lean-plan
      → lean-task
        → lean-implement (repeat per wave until done)
```

## Design principles

- **Extract, don't interrogate** — every skill pulls answers already available in the conversation or in the prior file before asking the user anything; only genuine gaps get a question, asked all at once
- **Each layer answers different questions** — domain/users/status, then problem/why/who, then what/how/impact/won't-do, then task breakdown, then code; no layer repeats what an earlier one already established
- **Frozen unless the thing itself changes** — `CONTEXT.md`, `SPEC.md`, and `PLAN.md` only get edited when their underlying reality changes, never to reflect progress; `TASKS.md` is the deliberate exception, since tracking progress is its entire job
- **Short is correct** — one page per file is a hard limit, not a suggestion; if it doesn't fit, the feature isn't scoped/planned/broken-down enough yet
- **Gaps are honest, guesses are risk** — an unanswered `[?]` is better than a confident assumption
