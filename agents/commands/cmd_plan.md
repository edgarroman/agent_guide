The user will provide a feature description. Your job is to:

1. Create a technical plan that concisely describes the feature the user wants to build.
2. Research the files and functions that need to be changed to implement the feature. Open the files you reference; do not infer from naming alone.
3. Before adding new code, audit `/src/utils`, `/src/uiElements`, and the relevant functional-area directory for existing utilities, components, or patterns that cover part of the work. Note what can be reused, extended, or deleted. Per `minimalism.md`, prefer subtractive or additive-and-isolated changes over cross-cutting rewrites.
4. Review project documentation `/docs` for any relevant guidelines and call out conflicts between the plan and documented patterns.
5. Avoid any product manager style sections (no success criteria, timeline, migration, etc).
6. Include specific and verbatim details from the user's prompt to ensure the plan is accurate.

This is strictly a technical requirements document that should:

1. Include a brief description to set context at the top.
2. Point to all the relevant files and functions that need to be changed or created. Mark each reference as `[verified]` (file was read) or `[assumed]` (referenced from naming, search hits, or memory only). The eval pass will target `[assumed]` references first.
3. Explain any algorithms that are used step-by-step.
4. Surface real decision points. When the plan picks one of multiple reasonable approaches (e.g., React Query vs. local state, new screen vs. extending an existing one, new utility vs. reusing one), note the choice, the rejected alternative, and the one-line reason. Do not surface trivial choices.
5. If necessary, break up the work into logical phases. Ideally this should be done with an initial "data layer" phase that defines the types and db changes that need to run, followed by N phases that can be done in parallel (e.g. Phase 2A - UI, Phase 2B - API). Only include phases if it's a REALLY big feature.
6. End with a short **Confidence and open questions** section with three buckets:
   - **High confidence** — claims grounded in files you read and patterns you verified.
   - **Medium confidence** — algorithms, data flows, or integration points you reasoned about but did not fully trace.
   - **Open questions** — genuine forks the user needs to resolve before implementation. Do not invent uncertainty; if there are none, say so.

**Flag any new UI component, construct, or pattern prominently.** Introducing a new reusable `Ui*` primitive, a new UI construct, or a novel interaction/layout/navigation pattern not already established in `src/uiElements/` or an existing screen may be an outlier that warrants careful human review — call it out on its own line with a `NEW UI:` marker (e.g. `NEW UI: <what and why>`), separate from ordinary new files. Prefer composing existing primitives; justify any new primitive against the escalation ladder in `agents/project/ui-patterns.md` (when that file defines one) and note any project-specific definition-of-done (e.g. gallery/design-sync). Screen compositions built entirely from existing `Ui*` elements are **not** new constructs and do not need this flag. New projects use the `Ui*` prefix; do not introduce a different primitive prefix.

If the user's requirements are unclear, especially after researching the relevant files, you may ask up to 5 clarifying questions before writing the plan. If you do so, incorporate the user's answers into the plan. Residual uncertainty after writing belongs in the Open questions bucket, not in further pre-write questions.

Prioritize being concise and precise. Make the plan as tight as possible without losing any of the critical details from the user's requirements. Honest medium-confidence labels are preferred over confident-sounding prose that hides assumptions.

Write the plan into a `plans/<YYYY-MM>/<N>-<descriptive-name>.md` file, where `N` is a four-digit numeric slot. Numbering is per-month; only consider files in the current `plans/<YYYY-MM>/` folder.

**Plan-associated images:** default **do not** write images to disk. Use conversation images while drafting, then put durable observations in plan prose. Create `plans/<YYYY-MM>/<N>-assets/` only when the user **explicitly asks** to save or attach image(s) to the plan. Do not infer that a mockup or screenshot should be persisted. When saving, put files only under that `<N>-assets/` directory (same four-digit `N`), never Expo/`src` assets or a shared `plans/.../assets/` dump (and never anywhere Metro/`assetBundlePatterns` might ship them in the app binary). Link with a relative path. Delete `<N>-assets/` with the plan when digested or abandoned.

**Default (standalone plan): round up to the next ten.**

- Scan the current month folder for four-digit numeric prefixes and find the maximum.
- Round that maximum UP to the next multiple of 10 — even if the maximum is already a sub-plan slot. Examples: max `0063` → `0070`; max `0060` → `0070`; max `0071` → `0080`.
- Do NOT use "max + 1" for a standalone plan. A new plan is standalone unless it is explicitly a sub-plan of one named existing plan (see below).
- Do NOT fill historical gaps. If the folder has `0010`, `0030`, `0040`, the next standalone plan is `0050`, not `0020`.

**Sub-plans: fill the slots between tens.**

- A plan is a sub-plan ONLY if it cannot stand on its own without a specific named parent plan (e.g. it directly extends, critiques, or is a required sub-task of that exact plan). A bug fix, a follow-up, or merely touching the same feature area is NOT automatically a sub-plan.
- If in doubt, treat the plan as standalone and round up to the next ten.
- Sub-plans take the next free slot immediately after their parent: parent `0060` → sub-plans `0061`, `0062`, `0063`, leaving room up to `0069` before the next standalone lands at `0070`.
- Do not create new letter-suffix subplans like `0010a`; leave existing historical letter-suffix filenames unchanged.
