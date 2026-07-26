The user will provide a path to an existing plan file. Your job is to critically evaluate the plan and produce a refined version that is measurably better. The goal is to ensure the plan is elegant, making the code more straightforward and simple instead of overengineered and complex.

Do NOT simply rewrite the plan. Work in distinct passes:

## Pass 1: Critique

Read the plan in full, then read the files and functions it references to verify the claims. If the plan uses `[verified]` / `[assumed]` markers, read every `[assumed]` reference and spot-check the `[verified]` ones; if it does not, read every reference. Produce a critique that identifies:

1. **Factual errors** - referenced files/functions that don't exist, incorrect descriptions of current behavior, wrong type signatures, or misunderstandings of how existing code works.
2. **Missing context** - relevant files, functions, call sites, or existing patterns the plan failed to discover. Search the codebase for adjacent code that should have been considered.
3. **Ambiguity** - steps where a competent engineer could implement two materially different things from the same description.
4. **Logical gaps** - unhandled edge cases, missing error paths, race conditions, migration ordering issues, or data flow steps that don't connect.
5. **Over/under-specification** - sections that go too deep on trivial mechanics, or sections that hand-wave the actually hard parts.
6. **Phase ordering issues** - work in a "parallel" phase that secretly depends on work in another parallel phase, or data layer changes that are missing prerequisites.
7. **Reuse and minimalism gaps** - existing utilities, components, or patterns in `/src/utils`, `/src/uiElements`, or the relevant functional-area directory that the plan reinvents; new abstractions introduced before a second caller exists; net-additive changes where a subtractive option was available. Reference `minimalism.md` when calling these out. **Explicitly check whether the plan introduces a new UI component, construct, or pattern** (a new `Ui*` primitive or a novel interaction/layout/navigation pattern rather than a composition of existing primitives); if so, verify it is flagged as a potential outlier per `agents/project/ui-patterns.md` ("Flag new UI constructs" when present) and challenge whether existing primitives would suffice.
8. **Project rule violations** - anything in the plan that conflicts with `/docs` guidelines or established patterns in the codebase.
9. **Calibration check** - if the plan has confidence buckets, validate them against what you found. A claim labeled "high confidence" that turns out to be wrong is a calibration failure worth flagging; a plan that labels everything "medium" to hedge is also a failure. Note any miscalibration plainly.

Be specific. "Section 3 is vague" is not useful. "Section 3 says 'update the auth middleware' but there are three middleware files in `src/middleware/auth/` and the plan doesn't say which" is useful.

## Pass 2: Alternatives

For the 2-3 most significant decisions in the plan, evaluate whether the chosen approach is actually the best one. Look for:

- Simpler approaches that achieve the same outcome
- Existing utilities/abstractions in the codebase that the plan reinvents
- Approaches that would require fewer file changes or less new code
- Approaches more consistent with how similar features are already implemented

If the plan surfaces explicit decision points (chosen vs. rejected alternative), validate that the rejection reason holds and that no obvious third option was missed. If an alternative is clearly better, note it. If the original choice is correct, do not change it.

## Pass 3: Refined plan

Rewrite the plan incorporating the fixes from Pass 1 and any improvements from Pass 2. The refined plan must:

1. Follow the exact same structure and constraints as `cmd_plan.md` (technical only, no PM sections, concise, file/function references, verified/assumed markers, decision points, confidence buckets, phased only if necessary).
2. Preserve every critical detail from the original user requirements verbatim where they appeared in the original plan.
3. Resolve every issue raised in Pass 1, or explicitly note why an issue was left unresolved.
4. Update verification markers and confidence buckets to reflect what the eval established. References you confirmed move from `[assumed]` to `[verified]`. Claims found to be wrong get fixed and their confidence downgraded or moved to open questions.
5. Not introduce new ambiguity or scope creep. If the refinement surfaces genuinely new requirements, list them in the open questions bucket rather than silently adding them.

## Output

Write the critique alongside the original plan (v2 can be incremented to v3, v4, etc. as there are subsequent revisions):

- `<original-number>-v2-critique-<original-name>.md` - the Pass 1 + Pass 2 output
- `<original-number>-v2-<original-name>.md` - the refined plan, **only if Pass 1 found material issues**. If Pass 1 found no material issues, write only the critique file with a clear statement to that effect, and do not produce a v2 plan.
- Leave the original plan untouched.

At the end of your response, print a short summary: how many issues were found by category, which alternatives (if any) were adopted, any calibration miscalls flagged, and any open questions the user needs to answer before implementation.

If after reading the plan and the referenced code you find no material issues, say so plainly and do not invent problems to justify a rewrite.
