# <Plan Title>

<!--
Blank template matching the structure required by agents/commands/cmd_plan.md.
Copy this file to plans/YYYY-MM/<N>-<descriptive-name>.md where N is the next
available number (zero-padded, monotonic across the whole project — not per
month). Delete these HTML comments before committing.
-->

## Description

<!-- One short paragraph: what is being built, in the user's own words where
possible. Set context only — no PM framing (no success criteria, timelines,
migration plans, etc.). -->

## References

<!-- Every file and function this plan will touch or rely on. Mark each as:
       [verified] — you opened and read the file
       [assumed]  — referenced from naming, search hits, or memory only
     The eval pass (cmd_eval.md) will hit every [assumed] reference first. -->

- `path/to/file.ts` — `functionName` [verified]
- `path/to/other.ts` — `OtherThing` [assumed]

## Reuse Audit

<!-- Per minimalism.md / cmd_plan.md step 3:
     - What existing utilities, components, or patterns cover part of this work?
     - What can be reused as-is, extended in place, or deleted as part of the change?
     - Prefer subtractive or additive-and-isolated changes over cross-cutting rewrites. -->

-

## Algorithm

<!-- Step-by-step. Be specific enough that a competent engineer couldn't
implement two materially different things from the same description. Show the
actual data flow, not just intent. -->

1.
2.
3.

## Decision Points

<!-- Only the real forks (e.g. React Query vs. local state, new screen vs.
extending an existing one). For each: chosen approach, rejected alternative,
one-line reason. Skip trivial choices. -->

- **<decision>**: chose <X> over <Y> because <one-line reason>.

## Phases (optional)

<!-- Only include if the work is genuinely big. Ideal shape:
       Phase 1 — data layer (types, schema, migrations)
       Phase 2A — UI
       Phase 2B — API / integration
     If it's not big enough to need phases, delete this section. -->

## Confidence and Open Questions

- **High confidence** — <claims grounded in files you read and patterns you verified>
- **Medium confidence** — <algorithms, data flows, or integration points you reasoned about but did not fully trace>
- **Open questions** — <genuine forks the user needs to resolve before implementation; say "none" if there are none, do not invent uncertainty>
