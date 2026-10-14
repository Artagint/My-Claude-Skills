# speed-skill

A repeatable loop for making the app faster without changing what users can do.

**How it works:** preflight reading, then a baseline of real timings you collect, then read-only research written into `SPEED.md` (database, edge, client, request flow). You answer the decisions, then fixes are built in measured slices ordered by gain, with typecheck, lint, tests and bundle-size checks after each. It closes by removing temporary measuring code, updating the docs and running [pr-review](../pr-review/). It never deploys or touches production settings itself.
