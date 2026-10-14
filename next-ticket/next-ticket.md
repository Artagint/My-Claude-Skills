---
description: Close out the current phase (rebase-merge its PR, mark its Linear ticket Done), set up the next one (In Progress, branch rebased on main) and write the handoff file for the next chat
argument-hint: "[PR number — defaults to the current branch's PR]"
---

Finish the current milestone phase and prepare the next. Milestones are split into phases (M1.1, M1.2, …), each with one Linear ticket, one branch named `feature/SCO-<id>-m<n>.<phase>-<slug>` and one PR (`docs/PROJECT_SPEC.md` §10 rule 14). Each phase gets its own chat (§10 rule 16): this command runs in the **old** chat, ends by writing `.claude/CONTEXT.md`, and the next chat starts with `/get-context`. Run the steps in order and stop to report if one fails — never force anything, never skip hooks, never discard work.

## 1. Merge the current phase's PR

1. Find the PR: `$ARGUMENTS` if a number was given (it must match `^[0-9]+$`), otherwise `gh pr view --json number` for the current branch. If there is none, stop and say so.
2. `gh pr view <n> --json state,mergeable,mergeStateStatus,headRefName,headRefOid,title,url`. Continue only if it is OPEN, `MERGEABLE` and `CLEAN`. GitHub computes mergeability lazily, so if either field is `UNKNOWN`, wait a few seconds and re-check, up to about 5 times. Otherwise stop and report why (conflicts, failing checks, draft, already merged).
3. Stop and list what you find, rather than losing it, if either of these is true:
   - `git status --short` shows uncommitted changes.
   - The local branch `<headRefName>` exists and, after `git fetch origin <headRefName>`, `git merge-base --is-ancestor <headRefName> <headRefOid>` fails. Show `git log --oneline <headRefOid>..<headRefName>`. Those are local commits the merge would drop, and the branch is force-deleted below. A local branch that's only behind the PR is fine.
4. `gh pr merge <n> --rebase --delete-branch`. Confirm the state is now MERGED.
5. `git checkout main`, `git pull`, `git fetch --prune`, and delete the local phase branch if it still exists (`git branch -D`). A rebase merge rewrites the commits, so `-d` would refuse; step 3 made the force safe.
6. Delete `.claude/pr-review-state/<owner>-<repo>-pr<n>.json` if it exists. The PR is merged, so there's nothing left to track.
7. Run `npm run stats` to regenerate `TOTAL_CODE.md`. If it changed, commit it directly to `main` (`git add TOTAL_CODE.md && git commit -m "Update code stats"`) and `git push`. It's a generated snapshot, not phase work, so it doesn't need its own branch or PR.

## 2. Close the current Linear ticket

The ticket id is the `SCO-<id>` in the merged branch name.

1. Set its state to **Done**.
2. Tick every checkbox in its description: patch `- [ ] ` → `- [x] ` with replace_all. If any item was genuinely not verified, still tick it, but name it in the final summary.
3. Attach the PR link if Linear hasn't already.

If Linear can't be reached, say so, skip this step and the Linear parts of step 3, and put it under "Not verified or not done" in the handoff so the next chat can finish it.

## 3. Set up the next phase

1. Find the next ticket: the one the closed ticket `blocks`. If there are several, or none, use the next `M<n>.<phase+1>` title in the same Linear project. Each milestone has its own project, "Scolara v1 M<n>". If the milestone has no phases left:
   - say so
   - set the finished milestone's project to Completed and the next milestone's project to In Progress
   - the handoff (step 4) then names the next **milestone** from `docs/PROJECT_SPEC.md` §8 instead of a ticket
   - offer to split it into phase tickets in that project, but don't create them unasked
2. Set the next ticket to **In Progress**.
3. Branch:
   - If `feature/SCO-<id>-…` exists locally, check it out and `git rebase main`.
   - If not, create it from `main` using the naming convention.
   - Don't push it; the first `/git-push` does that.
4. If phase branches for later tickets exist locally, leave them alone. They get rebased when their turn comes.

## 4. Write the handoff

Run `/handoff end-of-phase` (invoke it with the Skill tool, or read `.claude/commands/handoff.md` and follow it with the argument `end-of-phase`). It overwrites `.claude/CONTEXT.md` with what the next chat can't work out for itself; the format and the rules for what goes in it are there. Give it what steps 1 to 3 found: the merged PR, the ticket closed and any item ticked but unverified, the next ticket and branch, and anything that couldn't be done (a Linear update that failed, say).

## Report

Summarize in a few lines:
- the merged PR (URL) and the ticket closed, with any item ticked but unverified
- the ticket moved to In Progress
- the branch you're now on
- that `.claude/CONTEXT.md` is written

Then pass on `/handoff`'s closing instruction: open `.claude/CONTEXT.md`, read it, and say here what is missing or wrong, because this chat is the only place it can still be fixed; when happy, close this chat and start a new one with `/get-context`. Don't brief the next phase here; that is `/get-context`'s job.
