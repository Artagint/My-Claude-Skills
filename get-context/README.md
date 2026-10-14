# get-context

Starts a new chat on a project phase. Run `/get-context` as the first message.

**How it works:** reads `.claude/CONTEXT.md` (the handoff), checks its claims against the repo (branch, working tree, PR state, Linear ticket) and reports mismatches, reads the next ticket and the spec sections it cites, then briefs the phase: what it builds, what Claude does, what you do, open decisions and risks. It is read-only and never starts implementing until you answer.

Pairs with [handoff](../handoff/) and [next-ticket](../next-ticket/).
