# git-push

One command for the whole push workflow.

**How it works:** pulls (rebasing on divergence, never forcing), stages only the files actually changed, commits with a one-sentence message and no attribution lines, pushes, then handles the PR. The first push creates it with a Summary / What changed / Testing done description. Later pushes update only the sections the new commits affect. It never merges, closes, approves or comments on the PR.
