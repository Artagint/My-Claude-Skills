---
description: Audit and speed up the site: baseline, read-only research into SPEED.md, decisions, then fixes in measured slices, ending with cleanup, spec and review
---

A repeatable loop for making Scolara faster or lighter without changing what a student can do. It is what M3.12 (SCO-55) did, in order. Anything after `/speed-skill` in the message is the area to look at (a page, a flow, "uploads"); with none, take the flows that feel slow from the person.

**Rules that hold the whole way through**
- **Production behavior must not change.** A speed change that changes what a student sees or can do is a decision for the person, not an optimization. Deletes still look instant (spec §10 rule 15), creates and renames too (rule 17), every query stays scoped to `userId`, clients stay per request (`getPrisma`, `withPrisma`), and the viewer and download links never appear in a page's HTML.
- **No change without a baseline, no baseline without numbers.** Never call something faster from a guess; say what was measured and where.
- **Never deploy, change production settings, add a dependency without asking, or post to GitHub.** The person deploys (`npm run deploy` in `apps/web`) and approves each redeploy. Hyperdrive, placement and the like are configuration the person confirms.
- **Never put a database URL, key or other secret in a committed file.** Local addresses come from the root `.env` at run time (`apps/web/scripts/with-local-hyperdrive.mjs` is the pattern).
- **Temporary measuring code is listed in the plan and removed at the close.**
- Windows and PowerShell: prefer npm scripts. The shell strips backslashes, so write regexes, JSON and edit scripts with the Write tool and run them as `.mjs` files; the spec has CRLF and very long lines, so patch it with a script that checks each anchor matches exactly once.

## 1. Preflight

Read `CLAUDE.md`, `AGENTS.md` and the spec sections for the area (§5 data rules, §6 flows, §10 rules, §14 risks). AGENTS.md says this Next.js has breaking changes: read the relevant guide in `node_modules/next/dist/docs/` before judging any caching, prefetch or streaming behavior. Check the branch, `git status` and, if the work belongs to a phase, the Linear ticket (if the Linear tools are missing or fail, say so at once, per `CLAUDE.md`).

## 2. Baseline, run by the person

Ask for timings of the flows that feel slow, before any change: three runs each, in Chrome with DevTools open on the Network tab and "Disable cache" ticked, with screenshots. Two numbers per run: how long until the screen looks done, and the Time of the request the click started. Explain it in plain steps. The deployed site is the baseline, not `npm run dev`. If the person hasn't given numbers, do not start changing code.

## 3. Research: read-only, in one pass, no questions on the way

Write findings into `SPEED.md` as you go; anything you would have asked goes in its Questions section. Nothing in the app is changed in this phase. Check, in this order:

- **Database.** Every query the flows run against its index (`packages/db/prisma/schema.prisma`); N+1 and sequential awaits; whole rows where `select` would do. Supabase's performance advisor and `pg_stat_statements` (per-query time, lock waits) through the Supabase tools, read-only. Count the round trips of a click from the code: connection setup (about 7), then one per query, again for the page re-render a server action triggers.
- **Edge.** Worker placement (`apps/web/wrangler.jsonc`), Hyperdrive, `proxy.ts` and its session call, `getClaims` and the key fetch, what a request does before any data. A cold-start probe against the deployed `/login` (a page with no database work): one request after idle gaps of 5 to 180 s, then bursts of 8, from a script in the scratchpad.
- **Client.** A local `npx opennextjs-cloudflare build` in `apps/web`, then `node scripts/strip-baked-env.mjs` and `npx wrangler deploy --dry-run` for the size (against the 10 MiB compressed limit), and which client chunks each page loads (the `page_client-reference-manifest.js` files). Static file and `/pdfjs` headers with curl.
- **Request flow.** Next runs a tab's server actions one at a time, so read-only lookups as actions wait behind saves (they should be GET routes); polling that re-renders the page; `revalidatePath` invalidating every prefetched page; `<Link>` prefetch volume.
- **Looks faster.** Dialogs that wait on the server, uploads that show nothing until confirmed, pages with no `loading.tsx`.
- **The web, when needed** (Cloudflare, OpenNext, Next, Prisma and Supabase docs), for approaches known to work.

## 4. SPEED.md

Short version first, then: the person's baseline; where the time goes; what was measured (each with its number); findings ranked by gain, each with what happens, the evidence (file and line, or a measurement), the options, risk, effort and a **recommendation**; the person's guesses, checked (say plainly when a guess was wrong, as with indexes at M3.12); considered and not recommended; a proposed plan in slices; questions; sources. Double-check each finding against the real code before it goes in, and list every place a fact is stated.

## 5. Decide

Give the questions as a numbered list, each with a recommended option, and **stop**: do not build before they are answered. "Apply the recommendations" means all of them. Anything deferred goes into the spec (§8 M10) the same day.

## 6. Build in slices

One commit per slice (code, tests and the data layer together), ordered by measured gain, cheapest and safest first. After each slice: `npm run typecheck`, `npm run lint`, `npm test`, and the Worker build with the size check; a bundle that grows is a finding, not a detail (a plain `import()` of the PDF viewer once added 2.4 MB, and only `dynamic(..., { ssr: false })` keeps it out of the Worker).

- After an infrastructure slice (placement, a pooler, cache headers), stop and have the person deploy and re-time the baseline; choose the next slice from those numbers. Use a temporary `[timing]` log (`console.log` lines readable in the Worker's Observability tab) only when the numbers don't say where the time goes, and say it is temporary.
- Things that must stay true: **Hyperdrive with query caching off** (its cache is not cleared by writes); an optimistic create or rename first checks the form against the page's own names and counts and keeps its dialog open on a problem (the server stays the authority); pure checks live in `packages/core` with their own tests; a change measured as no gain is taken out.
- Call out anything that cannot be tested from here (no component tests exist; browser behavior) so it goes on the person's manual list.

## 7. Close

Remove the temporary measuring code and its tests; update the spec (what was built, the numbers, stale file lists and sections), `SPEED.md`'s status note, the Linear ticket's checklist and the PR description (only the parts that changed); run `/pr-review` and apply what it recommends; give the person a granular manual test list (where to click, what to see); list in the spec's M10 anything that was deferred. End with a short results table: each flow before and after, and the Worker size.
