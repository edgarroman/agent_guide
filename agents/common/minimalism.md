# Minimalism and Reuse

These rules bias agent-generated code toward _less_ code, not more. Work is effectively free to an LLM, so the default failure mode is overproduction: new utilities that duplicate existing ones, abstractions introduced before they are justified, refactors of files that were not part of the task, and scaffolding left behind after the task is done. Unchecked, this makes the system larger, not better. This guide is the counterweight.

Lines of code are a cost, not an output. Do not treat volume, file count, or breadth of changes as signs of progress.

## Core Principles

- **Prefer subtractive changes.** Before adding code, look for code you can delete. A diff that removes lines is usually safer than one that adds them.
- **Make the change easy, then make the easy change.** If the modification feels awkward, the first step is a small preparatory refactor — not piling the new logic on top.
- **Path independence.** Code should reflect the current requirements, not the order features were added. Ask: "If I rebuilt this file from scratch today, would it look like this?" If not, consider a cleanup pass before (or instead of) adding more.
- **YAGNI, the strict version.** Do not add parameters, options, generics, or abstraction layers in anticipation of a second caller. Wait for the second caller to actually exist.
- **Verification, not volume.** The expensive part of coding with an agent is being sure the result is right. Small, focused diffs are easier to verify than large ones — optimize for reviewability.

## Reuse Before Writing

- Before writing a utility, search `/src/utils`, `/src/uiElements`, and the relevant functional-area directory for something that already does the job. Grep for likely names and for the verbs in the task description
- If an existing utility covers 80% of the need, prefer calling it and handling the delta locally over generalizing it
- Check `agents/project/` (especially `ui-patterns.md` when present) for documented patterns before inventing one; consult specific `/docs/*.md` files only when those rules point you to one. Flag discrepancies between docs and code rather than silently diverging
- When unsure whether something exists, ask or search — do not assume it doesn't and write a new one

## Tolerance for Near-Duplicates

- Two short, simple functions that overlap slightly are preferred over one function with a `mode` parameter, a `config` object, or a conditional branch on a flag
- If consolidation requires adding a parameter that only one caller will ever pass `true` for, do not consolidate
- A shared helper earns its place on the third usage, not the second. The second usage can duplicate; the third is the signal to extract
- Naming matters more than deduplication. Two clearly-named 10-line functions beat one cleverly-named 25-line function

## Do Not Expand Scope

- Touch only the files required by the task. Do not refactor unrelated files because a "pattern" was spotted
- Do not rename symbols, reorder imports, or reformat files outside the scope of the change, even if they look wrong. Raise these as separate follow-ups
- Do not upgrade dependencies, adjust `tsconfig.json`, or change lint rules as a side effect of a feature task
- If a fix requires changes in an unrelated area, surface it and ask before expanding the diff

## Leave No Scaffolding Behind

- Delete exploratory files, scratch scripts, and throwaway harnesses before finishing. If a file was useful only for arriving at the answer, it does not belong in the final diff
- Do not check in near-duplicate assets (multiple variants of the same image, logo, or fixture). Pick one and delete the rest
- Zero-byte files, empty directories, and `TODO`-only stubs are always wrong. Remove them
- If a dependency, config file, or boilerplate was added while exploring but is not used by the final code, remove it
- Before declaring a task done, review the full diff — not just the files central to the change — and delete anything that is not earning its place

## Signals That the Wrong Fix Was Chosen

Treat these as red flags in generated code and in review:

- Reaching into a library's private members (leading underscore, `/internal/` paths, undocumented exports) to make something work
- Adding `// @ts-ignore`, `// @ts-expect-error`, `// eslint-disable`, or widening a type to `unknown`/`any` to silence an error rather than fix the cause
- Deleting or skipping a test because it failed after a change, without understanding why it failed
- Duplicating test fixtures rather than importing or factoring them
- Catching an error only to re-throw it with a less specific message
- Adding a parameter whose only job is to preserve old behavior for existing callers

When one of these appears, stop and look for a 5–20 line proper fix before shipping the 1-line shortcut.

## Abstractions

- Prefer concrete code over abstract code until the second real use case arrives. Premature abstractions outlive the problems they were invented for
- A good abstraction collapses code; it does not just relocate it. If extracting a helper produces roughly the same line count spread across more files, it is not earning its keep
- When introducing an abstraction, name it after the domain concept it represents, not the mechanism (`studentGrade`, not `dataProcessor`)
- Interfaces and layers of indirection are costs, not features. Add them when forced to, not by default

## Changes Should Be Reversible

- Prefer additive, easily-reverted changes over cross-cutting rewrites. If the change were wrong, how hard is it to roll back?
- A new feature behind a clear entry point (new file, new function) is easier to remove than the same feature woven through five existing files
- Avoid "drive-by" changes in unrelated files in the same change — they make reverts painful

## When in Doubt

- If two approaches seem reasonable, pick the one with fewer new files, fewer new exports, and fewer new concepts
- If the task is ambiguous enough that the choice of approach matters, ask before coding rather than guessing and building
- Writing less code is almost always cheaper than writing more and deleting it later
