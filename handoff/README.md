# handoff

Writes `.claude/CONTEXT.md`, the file a new chat reads with [get-context](../get-context/).

**How it works:** captures only what the next chat can't work out from the code, git or the ticket: where things stand, progress so far, decisions made in chat, preferences, open questions, deferred items, what wasn't verified, and gotchas. Run it yourself mid-phase when a chat is filling up. [next-ticket](../next-ticket/) runs it with `end-of-phase`. It changes nothing else.
