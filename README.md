# Agent Guide

A portable set of AI-agent rules and reusable command prompts, plus the glue that makes them auto-activate in Cursor.

This guide was extracted from a real Expo / React-Native project (GradeCorner). Most files are concrete, working examples rather than blank templates — adapt them to your stack rather than starting from zero. The structural conventions (where files live, how `.cursor/rules` loads them, how `/plans` is organized) are the reusable part.

---

## Repo Layout

```
.
├── AGENTS.md                       # Always-on entry point read by Cursor, Codex, and most other agents
├── README.md                       # This file
├── .cursor/
│   └── rules/                      # Cursor-specific glob loaders (point at agents/**.md based on edited file)
│       ├── testing.mdc
│       ├── typescript.mdc
│       └── ui-patterns.mdc
├── .github/
│   └── PULL_REQUEST_TEMPLATE.md    # Referenced by agents/README.md; matches the commit/PR style in AGENTS.md
├── agents/
│   ├── README.md                   # Quick map of what lives in agents/
│   ├── common/                     # Language-level rules: TypeScript, testing, security, minimalism
│   │   ├── minimalism.md
│   │   ├── security.md
│   │   ├── testing.md
│   │   └── typescript.md
│   ├── project/                    # App-specific rules (these are the GradeCorner-specific files)
│   │   ├── gradecorner.md
│   │   └── ui-patterns.md
│   └── commands/                   # Reusable prompts invoked by the user (e.g. "follow cmd_plan.md")
│       ├── cmd_eval.md
│       ├── cmd_plan.md
│       ├── cmd_plan_digest.md
│       ├── cmd_review_code.md
│       └── cmd_write_docs.md
└── plans/
    ├── _template.md                # Blank skeleton matching cmd_plan.md's required structure
    └── 2026-05/
        └── 0001-example-feature.md # Short worked example showing the plan format
```

---

## How The Pieces Fit Together

- **`AGENTS.md`** is the always-on entry point. Cursor, OpenAI Codex / codex-cli, and most modern agents read it on every turn. Keep it short and structural — point at `agents/**` for the substance.
- **`agents/common/`** holds language-level rules that aren't project-specific (TypeScript style, testing approach, security defaults, the minimalism / reuse guidelines).
- **`agents/project/`** holds project-specific rules (architecture, state management, component library conventions). Expect to rewrite these per project.
- **`agents/commands/`** holds reusable prompts. The user invokes them by name ("use `cmd_plan.md` to spec this out") rather than the agent loading them automatically.
- **`.cursor/rules/*.mdc`** are thin glob-scoped loaders. When Cursor sees a file matching the `globs:` frontmatter, it loads the referenced `agents/**.md` into context. This is what makes per-file-type guidance fire without bloating every prompt.
- **`plans/YYYY-MM/`** is where `cmd_plan.md` writes specs. Filenames are `NNNN-descriptive-name.md` with `NNNN` zero-padded and monotonically increasing within the project (not within the month).

---

## Install Into Your Project

Pick whichever option matches how tightly you want to track upstream changes.

### Option 1 — Copy (simplest, no coupling)

```sh
cp -R _agent_guide/{AGENTS.md,agents,.cursor,.github,plans} <your-repo>/
```

Then edit the project-specific files (see "Adapt These Files First" below). This is the right choice for most teams — agent rules drift per project anyway.

### Option 2 — Git subtree (track upstream, no submodule complexity)

```sh
git subtree add --prefix=agent-guide <git-url> main --squash
```

To pull updates later:

```sh
git subtree pull --prefix=agent-guide <git-url> main --squash
```

Subtree gives you a normal-looking subdirectory in your repo with the option to merge in upstream improvements. Local edits inside `agent-guide/` are preserved across pulls (with the usual merge conflicts).

### Option 3 — Git submodule (pinned shared source-of-truth)

```sh
git submodule add <git-url> agent-guide
git submodule update --init
```

Use this when multiple repos need to share a single canonical version pinned by SHA. Heavier workflow (`git submodule update --remote` on every machine, CI must clone recursively), so only pick it if you actually need that pinning.

After Option 2 or 3, symlink the top-level files Cursor / Codex expect at the repo root:

```sh
ln -s agent-guide/AGENTS.md AGENTS.md
ln -s agent-guide/.cursor/rules .cursor/rules
```

(Or copy them — agents only read from the repo root, not from arbitrary subdirectories.)

---

## Adapt These Files First

Everything in this repo is real working content, not generic boilerplate. Expect to edit:

- **`AGENTS.md`** — `yarn` commands, `src/app/` / `src/uiElements/` / `src/gradeBookSystems/` paths, references to `/docs/TimeStampHandlingGuide.md` and `/docs/LoggingGuide.md`. Replace with your stack.
- **`agents/project/gradecorner.md`** — entirely GradeCorner-specific (Drizzle + SQLite, expo-router, React Query, i18next, the gradebook integration layer). Rewrite for your architecture; the section headings (Architecture, Folder Structure, State Management, Localization, Workflow) transfer well.
- **`agents/project/ui-patterns.md`** — GradeCorner-specific component layer (`GCView`, `GCText`, `react-native-paper` wrappers, safe-area handling). Replace with your component-library conventions.
- **`agents/common/minimalism.md`** — mostly generic, but references `/src/utils`, `/src/uiElements`, `agents/project/gradecorner.md`, and `agents/project/ui-patterns.md` by name. Update the paths to match your layout.
- **`agents/common/typescript.md`** — generic TypeScript rules, but assumes Expo / React-Native / Expo Router (default exports allowed only in `src/app/`, `withRouteData`, etc.). Strip Expo-isms if you're not on Expo.
- **`agents/common/testing.md`** — assumes Jest + React Native Testing Library + Detox. Swap for your test stack.
- **`agents/common/security.md`** — assumes `react-native-encrypted-storage`, Zod, Sentry. Mostly transferable; swap the storage primitive.
- **`agents/commands/cmd_plan.md`** and **`cmd_eval.md`** — reference `/src/utils`, `/src/uiElements`, `/docs`, `agents/project/gradecorner.md`, `agents/project/ui-patterns.md`. Update paths so the prompts point at the right directories in your repo.
- **`.cursor/rules/*.mdc`** — `globs:` patterns assume `src/screens/`, `src/uiElements/`. Adjust to your folder layout.
- **`.github/PULL_REQUEST_TEMPLATE.md`** — references `yarn test` / `yarn lint`. Swap for your commands.

When in doubt: search the copied files for `yarn`, `expo`, `gradecorner`, `aspen`, `GC`, `/src/`, `/docs/` — these are the heavy markers.

---

## Tool Compatibility

The primary target is **Cursor**. The same files work, with varying levels of automation, in other agents.

| Tool | What it reads automatically | What you need to do |
|---|---|---|
| **Cursor** | `AGENTS.md` (always-on), `.cursor/rules/*.mdc` (glob-scoped) | Nothing — drop-in. |
| **OpenAI Codex / codex-cli** | `AGENTS.md` (always-on) | Nothing. `.cursor/rules/*.mdc` are ignored — embed key rules inline in `AGENTS.md` if you want them always-on for Codex. |
| **Claude Code** | `CLAUDE.md` from repo root | `ln -s AGENTS.md CLAUDE.md` (or duplicate the file). Claude Code has no glob-loader equivalent — either reference `agents/**.md` from `CLAUDE.md` and rely on on-demand loads, or inline the rules. |
| **Aider, Cline, Continue, etc.** | Varies; most accept a system-prompt file or read `AGENTS.md` | Point them at `AGENTS.md`; they'll discover `agents/**` via grep. |

The cross-cutting principle: `AGENTS.md` is the lowest-common-denominator entry point. Keep it short, make it point at `agents/` for substance, and most agents will follow the links.

---

## Workflow Conventions Worth Adopting

These come from `AGENTS.md` and `agents/commands/` and are worth keeping even if you rewrite the substance:

- **Specs live under `/plans/YYYY-MM/`.** Filenames are `NNNN-descriptive-name.md` (zero-padded, monotonic). See `agents/commands/cmd_plan.md` for the format and `plans/2026-05/0001-example-feature.md` for a worked example.
- **Plan eval pass.** After writing a plan with `cmd_plan.md`, run `cmd_eval.md` to critique and refine it into a v2. The two-pass workflow catches assumed file references and lets you mark each as `[verified]` vs `[assumed]`.
- **Plan digest at month rollover.** `cmd_plan_digest.md` compacts the previous month into `_digest.md`, keeping active investigations and pointing at git history for the rest.
- **Git safety.** `AGENTS.md` includes a strict policy that agents must not run mutating git commands without explicit per-turn approval. Keep this verbatim if you want the same behavior.
- **PR template.** `.github/PULL_REQUEST_TEMPLATE.md` matches the commit / PR style described in `AGENTS.md`. GitHub picks it up automatically.

---

## License

TODO — add a `LICENSE` file before publishing. This staging area was extracted from a private project; pick a license (MIT, Apache-2.0, or proprietary) before pushing to a public remote.
