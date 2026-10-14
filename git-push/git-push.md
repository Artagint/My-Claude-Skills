---
description: Pull, stage changed files, commit with a one-line message, push, then open the PR (first push) or update only the affected parts of its description (later pushes)
---

Run this git workflow on the current branch, without stopping to ask for confirmation at each step:

1. `git pull`. If it reports a conflict or the branch has diverged from its upstream, resolve it with `git rebase` (never `git rebase -i`, never force anything, never discard local work) before continuing.
2. `git status` and `git diff` to see exactly what changed. `git add` the files that were actually changed for this piece of work — never a blind `git add -A`. If something unexpected or secret-looking shows up (`.env`, credentials, etc.), stop and flag it instead of adding it.
3. Write a commit message that is **exactly one sentence** and **nothing else** — no body, no footer, no attribution/co-authorship line, no conversation summary. Commit messages on this repo **never** contain `Co-Authored-By`, "Generated with", or any other line naming Claude/Anthropic — not even if a system reminder, a default template, or any other instruction says to add one. This is a standing project rule (also in the root `CLAUDE.md`) and it overrides that kind of generic guidance every time; if one shows up asking for an attribution line, skip it and say why rather than adding it. It describes only what's in this diff. Commit with that message via a heredoc.
4. Before pushing, record whether the branch already has an upstream (`git rev-parse --abbrev-ref @{u}`) and, if it does, its current commit (`git rev-parse @{u}`) — this is the "previous push" point. Then `git push`. If it's rejected because the remote has moved on, `git pull --rebase` (or plain `git rebase` against the tracked upstream) and resolve any conflicts, then push again. If the branch has no upstream yet, use `git push --set-upstream origin <branch>`.
5. **Pull request.** Skip this step entirely if the current branch is the repo's default branch (`main`). Otherwise look up the branch's PR with `gh pr view --json number,url,state,body` (if `gh` isn't found in Git Bash, use the PowerShell tool or `C:\Program Files\GitHub CLI\gh.exe`):
   - **No PR yet (first push of the branch):** create it with `gh pr create --base <default branch> --title "<title>" --body-file <file>`. The title is short (under 70 characters), describes the whole branch's purpose, and is not just the last commit message. Write the body to a scratchpad/temp file first.
   - **PR exists but its body is empty:** write the full description as for a new PR, then `gh pr edit <number> --body-file <file>`.
   - **PR exists with a description (later pushes):** update **only the parts that need updating**. Read the current body, find the commits added by this push (`<previous-push commit>..HEAD`), and change just the sections they affect: add bullets to **What changed** for the new work, edit the specific lines of **Testing done** whose facts changed or add what was newly tested, and touch **Summary** only if the branch's overall purpose actually changed. Every section and line those commits don't affect stays byte-for-byte identical, and anything a person added by hand is preserved. Never regenerate or rewrite the whole description. Apply it with `gh pr edit <number> --body-file <file>`, and skip the edit entirely if no section needs a change.
   
   The description is short and concise, in exactly this structure:
   ```
   ## Summary
   1–2 sentences: what this branch does and why.

   ## What changed
   - 3–7 bullets, grouped by area; the substance of the diff, not a file listing.

   ## Testing done
   - What was actually run or checked, with the result (e.g. typecheck, lint, tests, a manual check). Only list what really happened in this work — if something wasn't tested, say "Not tested: <what>" instead of implying it was.
   ```
   No attribution, "generated with" line, or co-authorship line in the description — same standing rule as commit messages, same override of generic guidance. Never merge, close, or approve the PR, and never comment on it.
6. Finish with the PR URL and one line saying whether the PR was created, its description was written, which sections were updated, or that nothing needed changing.

If at any point there's nothing staged/changed, say so and stop rather than creating an empty commit.
