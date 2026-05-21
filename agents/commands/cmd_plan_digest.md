The user will name a target month folder, usually `plans/YYYY-MM/`. Your job is
to compact that folder into a durable `_digest.md` while preserving rationale
and avoiding broken references.

1. Inventory `plans/YYYY-MM/*.md`.
   - Collapse versioned files by topic number when possible.
   - Treat date-style files as one topic per date slug.
   - Recompute counts at execution time; do not trust old plans.
2. Re-run `rg "plans/YYYY-MM" -g '!plans/**'` to find source/doc anchors.
   - If the underlying work is still active, keep the plan and add a one-line
     retained status at the top.
   - If settled, move durable "why" into existing `/docs` files or local source
     comments, then remove or repoint the plan reference.
   - If a referenced plan is already missing, recover from git history when
     possible; otherwise document from current source behavior.
3. Write `plans/YYYY-MM/_digest.md`.
   - Include the observed file count and the shared recovery recipe:
     `git log -- plans/YYYY-MM/<glob>.md`, then
     `git show <sha>:plans/YYYY-MM/<file>.md`.
   - Use one concise entry per topic with status, summary, durable rationale
     destination, and recovery glob.
   - Mark retained active plans explicitly.
4. Delete compacted plan files only after anchors are resolved.
   - Keep `_digest.md` and active retained plans.
   - Do not delete a plan that is still referenced unless the reference is an
     intentional active-plan anchor.
5. Verify with:
   - `ls plans/YYYY-MM/`
   - `rg "plans/YYYY-MM" -g '!plans/**'`
   - Spot-check at least three recovery recipes with `git log` / `git show`.

Prefer existing docs over new docs, and keep salvaged rationale to a few
sentences. The digest is an index; git is the archive.
