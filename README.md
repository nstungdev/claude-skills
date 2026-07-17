# claude-skills
Claude skills to work faster, smarter, and save more time and cost

## Installation

This repo is a Claude Code plugin marketplace. To use a plugin (e.g. `dev-toolkit`) in any project on your machine:

```bash
# From a local clone (no push needed):
claude plugin marketplace add /path/to/claude-skills
/plugin install dev-toolkit@claude-skills

# Or from GitHub, once pushed:
claude plugin marketplace add nstungdev/claude-skills
/plugin install dev-toolkit@claude-skills
```

Add `--scope user` to `marketplace add` to make it available in every project automatically, rather than just the current one.

After updating a skill in this repo, run `/plugin marketplace update` in any session to pick up the changes.

## Plugins

| Plugin | Description |
|--------|--------------|
| [`dev-toolkit`](skills/dev-toolkit) | Lean context/spec/plan/task/implement chain for carrying a feature from project context to working code |
