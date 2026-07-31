# Agent Guide

A portable set of AI-agent rules and reusable command prompts, plus the glue that makes them auto-activate in Cursor.

This guide was extracted from a real Expo / React-Native project (GradeCorner). Most files are concrete, working examples rather than blank templates — adapt them to your stack rather than starting from zero. The structural conventions (where files live, how `.cursor/rules` loads them, how `/plans` is organized) are the reusable part.

**Package manager:** prefer **bun** for new projects and for command examples in this repo. Do **not** migrate an existing yarn (or npm) install just to match the guide — keep that project’s lockfile and tooling; only swap command strings when the project already uses bun.

**UI primitives:** new projects use the **`Ui*`** prefix under `src/uiElements/`. GradeCorner sample files under `agents/project/` may still show historical `GC*` naming; that is not the portable default for new installs.

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
│   ├── project/                    # App-specific rules (GradeCorner samples — rewrite on install)
│   │   ├── gradecorner.md
│   │   └── ui-patterns.md
│   └── commands/                   # Reusable prompts invoked by the user (e.g. "follow cmd_plan.md")
│       ├── cmd_eval.md
│       ├── cmd_plan.md
│       ├── cmd_plan_digest.md
│       ├── cmd_review_code.md
│       └── cmd_write_docs.md
└── plans/
    ├── _template.md                # Optional blank skeleton; cmd_plan.md is authoritative
    └── 2026-05/
        └── 0010-example-feature.md # Short worked example (skip-by-10 numbering)
```

---

## How The Pieces Fit Together

- **`AGENTS.md`** is the always-on entry point. Cursor, OpenAI Codex / codex-cli, and most modern agents read it on every turn. Keep it short and structural — point at `agents/**` for the substance.
- **`agents/common/`** holds language-level rules that aren't project-specific (TypeScript style, testing approach, security defaults, the minimalism / reuse guidelines). Pointers stay at the `agents/project/` directory, not a hard-coded project filename.
- **`agents/project/`** holds project-specific rules (architecture, state management, component library conventions). Expect to rewrite these per project.
- **`agents/commands/`** holds reusable prompts. The user invokes them by name ("use `cmd_plan.md` to spec this out") rather than the agent loading them automatically.
- **`.cursor/rules/*.mdc`** are thin glob-scoped loaders. When Cursor sees a file matching the `globs:` frontmatter, it loads the referenced `agents/**.md` into context. This is what makes per-file-type guidance fire without bloating every prompt.
- **`plans/YYYY-MM/`** is where `cmd_plan.md` writes specs. Filenames are `NNNN-descriptive-name.md` with `NNNN` zero-padded. Numbering is **per-month**. Standalone plans **round up to the next ten**; sub-plans fill the slots between tens. See `agents/commands/cmd_plan.md`. `plans/_template.md` is an optional skeleton; if it disagrees with `cmd_plan.md`, trust the command.

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

- **`AGENTS.md`** — paths under `src/`, references to `/docs/TimeStampHandlingGuide.md` and `/docs/LoggingGuide.md`, and any sample feature-area folders. Command examples prefer bun; if the project still uses yarn, substitute yarn scripts (do not migrate the package manager just to match this guide).
- **`agents/project/gradecorner.md`** (rename) — entirely GradeCorner-specific sample. Rewrite for your architecture; the section headings (Architecture, Folder Structure, State Management, Localization, Workflow) transfer well.
- **`agents/project/ui-patterns.md`** — replace GradeCorner’s historical `GC*` / Paper sample with your `Ui*` conventions.
- **`agents/common/minimalism.md`** — mostly generic; it already points at `agents/project/` rather than a named project file. Update `/src/utils` / `/src/uiElements` paths if your layout differs.
- **`agents/common/typescript.md`** — generic TypeScript rules, but assumes Expo / React-Native / Expo Router (default exports allowed only in `src/app/`, `withRouteData`, etc.). Strip Expo-isms if you're not on Expo.
- **`agents/common/testing.md`** — assumes Jest + React Native Testing Library + Detox. Swap for your test stack.
- **`agents/common/security.md`** — assumes `react-native-encrypted-storage`, Zod, Sentry. Mostly transferable; swap the storage primitive.
- **`agents/commands/cmd_plan.md`** and **`cmd_eval.md`** — reference `/src/utils`, `/src/uiElements`, `/docs`, `agents/project/ui-patterns.md`. Update paths so the prompts point at the right directories in your repo.
- **`.cursor/rules/*.mdc`** — `globs:` patterns assume `src/screens/`, `src/uiElements/`. Adjust to your folder layout.
- **`.github/PULL_REQUEST_TEMPLATE.md`** — prefers `bun run test` / `bun run lint`. Yarn projects substitute their scripts.

When in doubt: search the copied files for `yarn`, `expo`, `gradecorner`, `aspen`, `GC`, `/src/`, `/docs/` — these are the heavy markers. For new UI work, prefer `Ui*` over any remaining `GC*` samples.

---

## What Is Portable vs Project-Owned

When syncing improvements back into this guide (or pulling the guide into an app):

- **Portable** — `agents/commands/*`, `agents/common/*` (keep `agents/project/` directory pointers only), plan numbering / images / `NEW UI:` / `Ui*`, Git Safety including the Cloud Agent Exception, `plans/_template.md`, PR template shape, prefer-bun-don’t-migrate-yarn.
- **Project-owned** — `AGENTS.md` structure and paths, substance of `agents/project/*`, `.cursor/rules` globs and reminder bullets tuned per app, plus extras (MCP, simulator rules, `CLAUDE.md`, etc.).

Typical sync mechanism: a local clone edited and pushed to this repo (copy/subtree/submodule are install options for consumers).

---

## Tool Compatibility

The primary target is **Cursor**. The same files work, with varying levels of automation, in other agents.

| Tool                             | What it reads automatically                                  | What you need to do                                                                                                                                                                                 |
| -------------------------------- | ------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cursor**                       | `AGENTS.md` (always-on), `.cursor/rules/*.mdc` (glob-scoped) | Nothing — drop-in.                                                                                                                                                                                  |
| **OpenAI Codex / codex-cli**     | `AGENTS.md` (always-on)                                      | Nothing. `.cursor/rules/*.mdc` are ignored — embed key rules inline in `AGENTS.md` if you want them always-on for Codex.                                                                            |
| **Claude Code**                  | `CLAUDE.md` from repo root                                   | `ln -s AGENTS.md CLAUDE.md` (or duplicate the file). Claude Code has no glob-loader equivalent — either reference `agents/**.md` from `CLAUDE.md` and rely on on-demand loads, or inline the rules. |
| **Aider, Cline, Continue, etc.** | Varies; most accept a system-prompt file or read `AGENTS.md` | Point them at `AGENTS.md`; they'll discover `agents/**` via grep.                                                                                                                                   |

The cross-cutting principle: `AGENTS.md` is the lowest-common-denominator entry point. Keep it short, make it point at `agents/` for substance, and most agents will follow the links.

---

## Workflow Conventions Worth Adopting

These come from `AGENTS.md` and `agents/commands/` and are worth keeping even if you rewrite the substance:

- **Specs live under `/plans/YYYY-MM/`.** Per-month four-digit slots. Standalone plans round up to the next ten; sub-plans fill intervening slots. See `agents/commands/cmd_plan.md` and `plans/2026-05/0010-example-feature.md`.
- **Plan images default off disk.** Persist under `plans/YYYY-MM/<N>-assets/` only when the user explicitly asks.
- **Plans close with Expected outcome, then Confidence.** `cmd_plan.md` requires a short observable end-state section before the confidence buckets; `cmd_eval.md` checks outcome conformance (missing section, unproduced claims, missing ask coverage, scope creep).
- **Plan eval pass.** After writing a plan with `cmd_plan.md`, run `cmd_eval.md` to critique and refine it into a v2. The two-pass workflow catches assumed file references and lets you mark each as `[verified]` vs `[assumed]`.
- **Plan digest at month rollover.** `cmd_plan_digest.md` compacts the previous month into `_digest.md`, keeping active investigations and pointing at git history for the rest (deleted plan text remains recoverable via `git log`).
- **Git safety.** `AGENTS.md` includes a strict policy that agents must not run mutating git commands without explicit per-turn approval, plus a Cloud Agent Exception for Cursor cloud PR flows. Keep this if you want the same behavior.
- **PR template.** `.github/PULL_REQUEST_TEMPLATE.md` matches the commit / PR style described in `AGENTS.md`. GitHub picks it up automatically.
