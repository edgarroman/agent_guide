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

If the user's requirements are unclear, especially after researching the relevant files, you may ask up to 5 clarifying questions before writing the plan. If you do so, incorporate the user's answers into the plan. Residual uncertainty after writing belongs in the Open questions bucket, not in further pre-write questions.

Prioritize being concise and precise. Make the plan as tight as possible without losing any of the critical details from the user's requirements. Honest medium-confidence labels are preferred over confident-sounding prose that hides assumptions.

Write the plan into a `plans/<YYYY-MM>/<N>-<descriptive-name>.md` file with N being the next available feature number (starting with 0001).
