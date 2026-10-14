---
description: Start a new chat for the next phase: read the handoff file, check it against the repo, then brief what the phase needs
---

Catch up a new chat on the phase `/next-ticket` just set up. `.claude/CONTEXT.md` is what the previous chat wrote down (`docs/PROJECT_SPEC.md` §10 rule 16). This command only reads and reports: it never edits, commits, checks out a branch or changes Linear, so it is safe to run again whenever a chat needs catching up, for example after reopening one mid-phase.

## 1. Read the handoff

Read `.claude/CONTEXT.md`. A file with a "Progress in this phase" section was written mid-phase by `/handoff`: it describes a phase that is already under way, so in step 4 brief what is left, not the whole phase. Otherwise it was written by `/next-ticket` for a phase that hasn't started. If it doesn't exist, say so and carry on from step 2, taking the phase from the current branch name and the next unfinished ticket; the briefing then rests on the ticket and the spec alone.

## 2. Check it against the repo

Its claims can be stale, so look at what is actually there, and report every mismatch instead of fixing it:

- `git branch --show-current`, `git status --short` and `git log --oneline -5`: is the checkout on the branch the file names, with the working tree it describes? If the branch has commits beyond the base the file names, the phase has already started, so say that.
- `gh pr view <n> --json state` for the PR the file says was merged: is it MERGED? If the file names a phase PR that is already open, give its state.
- Whether the next ticket's state in Linear matches (In Progress). If Linear can't be reached, say so and use `docs/PROJECT_SPEC.md` §8 for the ticket's content.

Don't switch branches. If the checkout is on the wrong one, say which command would fix it and ask.

## 3. Read the phase

Read the next ticket (task, done-when, out of scope, notes, references) and the spec sections it cites. Skim what the previous phases left in the code where it matters, e.g. which files exist and what's stubbed. Auto-memory loads by itself; don't repeat it.

## 4. Brief the phase

Start with two or three lines on what carried over from the handoff that affects this phase, and any mismatch from step 2. Then give a short breakdown:

- **What this phase builds:** the concrete pieces, as a numbered list.
- **My part:** the steps Claude can do.
- **Your part:** every step that needs the person, e.g. dashboard or account setup, secrets, a manual test or a purchase. Write it as a granular numbered walkthrough, one action per step:
  - where to go (URL, dashboard page, or terminal and folder)
  - exactly what to click, type or paste, with commands in code blocks
  - what they should see when it worked

  Group the steps by where they happen, and say which ones must come before Claude's work and which come after.
- **Decisions needed:** anything the ticket or spec leaves open, plus the handoff's "Open questions for Artem" and "Not verified or not done" items that belong to this phase, each with a recommended option. Ask before adding dependencies (CLAUDE.md).
- **Risks or gotchas** from the spec, the handoff's "Gotchas" and earlier phases' findings that apply here, e.g. Workers-specific limits.

End with the question of what to start on, or confirmation to begin. Don't start implementing until the person answers.
