---
description: Write .claude/CONTEXT.md, the handoff a new chat reads with /get-context. Run it mid-phase when a chat is filling up; /next-ticket runs it at the end of a phase
argument-hint: "[end-of-phase — only /next-ticket passes this]"
---

Write the handoff for the next chat (`docs/PROJECT_SPEC.md` §10 rule 16). This command writes one file and changes nothing else: no commits, no branch changes, no Linear updates.

Overwrite `.claude/CONTEXT.md` (it is gitignored; the old content is replaced, not appended). It is the only thing the next chat knows about this one, so it holds **what the next chat can't work out for itself**. It can read the ticket, the spec, the code and git on its own, so don't copy those in; link to them (a spec section, a ticket id) instead. Don't repeat what is already in the auto-memory files either, and if something should outlive this phase, save it as a memory instead of putting it here. Never put secrets in it.

## Which kind

- `$ARGUMENTS` is `end-of-phase`: `/next-ticket` has merged the finished phase's PR, closed its ticket and set up the next branch. The file hands over the **next** phase, which hasn't started.
- Otherwise (the person ran `/handoff` themselves): the phase is **under way**. The file hands over the **current** phase, so a new chat can pick up where this one stops. Take the phase from the current branch name, check `git status --short`, `git log --oneline main..HEAD` and `gh pr view --json number,state,url` (no PR yet is fine) for the facts below, and include the "Progress" section.

## Format

Use exactly these headings, in this order. Write `None` under one that is empty. Keep the whole file under about 80 lines, in plain sentences, with the reason next to each decision.

```markdown
# Handoff to <the phase, e.g. M3.6 (SCO-51)>      (end of phase)
# Handoff within <the phase, e.g. M3.6 (SCO-51)>  (mid-phase)

Written <YYYY-MM-DD> at the end of <finished phase, e.g. M3.5 (SCO-47)>.   (end of phase)
Written <YYYY-MM-DD> partway through the phase.                             (mid-phase)

## Where things stand
End of phase:
- Merged: <PR number and URL>. Ticket closed: <SCO-id>, <items ticked but not verified, if any>.
- Next: <SCO-id, title, Linear URL> (or the next milestone, §8).
- Branch: `<branch>`, created from or rebased on `main` at <short sha>, not pushed.
- Working tree: <clean, or what is in it and why>.
Mid-phase:
- Ticket: <SCO-id, title, Linear URL>.
- Branch: `<branch>`, <number of commits ahead of main>, pushed or not.
- PR: <number, URL and state, or none yet>.
- Working tree: <clean, or what is uncommitted and why>.

## Progress in this phase
Mid-phase only; leave this heading out at the end of a phase. What is done (name the commits or files that matter), what is left, and the step in progress, so the new chat can carry on without redoing anything.

## Decisions made in chat
Decisions and answers that aren't in the spec or the ticket yet, each with its reason.

## How Artem wants this done
Preferences and corrections from this phase that aren't in CLAUDE.md or a memory file.

## Open questions for Artem
Questions to ask at the start of the next chat, each with what we recommended and any reminder he asked for.

## Deferred
Things postponed, each with where it is recorded (a spec section or a ticket), so nothing lives only here.

## Not verified or not done
What was checked only in part (and how), and anything that couldn't be done: a Linear update, a deployed check, a test that wasn't run.

## Gotchas
Things that cost time this phase and would again: tool quirks, environment state, traps in the code.
```

Carry forward anything still open from the previous `.claude/CONTEXT.md` if this chat started from one. Dropping a still-open question loses it for good.

## Report

Say in one line that `.claude/CONTEXT.md` is written, then tell the person to open it, read it, and say in this chat what is missing or wrong, because this chat is the only place it can still be fixed. When they're happy, they close this chat and start a new one with `/get-context`.
