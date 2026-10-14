# pr-review

Multi-agent review of a GitHub PR: `/pr-review <number> [--repo owner/name]`.

**How it works:** validates the PR, gathers the diff and CI status, then runs up to three parallel reviewers (correctness and security, performance and cost, conventions and hygiene). Each finding is checked by adversarial verifiers, with two for bugs and one for "worth confirming". Output is numbered findings by severity plus recommendations. Review state is saved per PR so re-reviews only look at what changed. It is read-only and never posts to GitHub; you paste comments yourself.
