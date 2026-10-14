# My Claude Skills

Custom slash commands for [Claude Code](https://claude.com/claude-code), written for my AI study app Scolara. Each skill lives in its own folder with a short README and the command file itself.

| Skill | What it does |
|---|---|
| [get-context](get-context/) | Catches a new chat up from the handoff file and briefs the phase |
| [git-push](git-push/) | Pull, commit, push, then open or update the PR |
| [handoff](handoff/) | Writes the handoff file the next chat reads |
| [next-ticket](next-ticket/) | Merges a finished phase, closes its ticket, sets up the next |
| [pr-review](pr-review/) | Multi-agent PR review with adversarial verification |
| [speed-skill](speed-skill/) | A measured loop for making the app faster |

## Install

Copy a skill's `.md` file into `.claude/commands/` in your project (or `~/.claude/commands/` for all projects). The file name becomes the command, e.g. `/git-push`.

## Note

These were built around one project's workflow. They refer to its spec (`docs/PROJECT_SPEC.md`), Linear tickets, and conventions, so adapt those parts before using them elsewhere.
