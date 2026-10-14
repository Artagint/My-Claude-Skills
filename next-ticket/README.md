# next-ticket

Closes out the current phase and sets up the next one.

**How it works:** rebase-merges the phase's PR (after checking it is open, clean and loses no local commits), marks its Linear ticket Done and ticks its checklist, moves the next ticket to In Progress, creates or rebases its branch on `main`, then runs [handoff](../handoff/) so the next chat can start with [get-context](../get-context/). It stops and reports if any step fails and never forces anything.
