---
description:
alwaysApply: true
---

# Repository Guidelines

## Agent Rules

`/agents` contains all coding standards and project guidance. `.cursor/rules/*.mdc` files use globs to load the right rules for the files being edited.

- `agents/common/` — language-level rules: TypeScript style, testing, security, minimalism
- `agents/project/` — project-specific rules: architecture, UI patterns, state management
- `agents/commands/` — reusable prompts for planning, code review, and documentation

`/docs/*.md` holds topic-specific architecture references; the `agents/` rules above and the `README.md` files in major `src/` subdirectories link to them when relevant. Repo-wide conventions worth knowing up-front: `/docs/TimeStampHandlingGuide.md` (`...EpochSec` / `DateString` naming) and `/docs/LoggingGuide.md` (`Log` / `LogSubSys` over `console.*`). `/plans/YYYY-MM/` holds dated specs and proposals.

## Repository Structure

- `src/app/` contains Expo Router entries; route files should stay thin and delegate UI to `src/screens/`.
- `src/uiElements/` contains shared theme-aware `Ui*` primitives and wrappers (only this folder should import the underlying UI kit when the project uses that pattern).
- `src/gradeBookSystems/` contains gradebook integrations and related parsing/auth logic. *(Sample path from the GradeCorner extraction — replace with your feature areas.)*
- Tests and Jest setup live in `test/`, `jest.config.js`, and `jest.setup.*.js`.

## Build, Test, and Development Commands

Prefer **bun** for new projects and for the command examples below. Existing yarn (or npm) installs should keep their package manager until the project chooses to migrate — this guide does not require a package-manager migration.

- `bun install` — install dependencies.
- `bun run start`, `bun run ios`, `bun run android`, `bun run web` — run the app via Expo Dev Client/native runners/web.
- `bun run test`, `bun run test:ci` — execute Jest locally or in CI.
- `bun run lint` — ESLint (+ typecheck if configured) for `src`.
- `bun run db:generate` — regenerate Drizzle SQL artifacts after schema edits.

If the repo still uses yarn, substitute `yarn <script>` for `bun run <script>` in this file and in `.github/PULL_REQUEST_TEMPLATE.md`.

## Workflow Notes

- For implementation specs or proposals, write to `/plans/YYYY-MM/` (see `agents/commands/cmd_plan.md`).
- Standalone plan numbers round up to the next ten within the month; sub-plans fill the slots between tens (see `cmd_plan.md`).
- Plan images: do not save to disk unless the user explicitly asks; see `agents/commands/cmd_plan.md`. When persisted, only `plans/YYYY-MM/<N>-assets/` (never Expo/`src`). Delete with the plan when digested.
- Plan files in `plans/YYYY-MM/` use rolling retention. At month rollover, run `agents/commands/cmd_plan_digest.md` to compact the previous month into `_digest.md`, while retaining active investigations until they settle. Full deleted plan text remains recoverable via `git log -- plans/YYYY-MM/<file>`.
- Coding and workflow guidance lives in `/agents`; `/docs` is referenced from there when relevant.

## Git Safety (always)

- Agents MUST NOT run write/mutation git commands on their own. This includes, at minimum: `git commit` (incl. `--amend`), `git push` (incl. `--force`/`--force-with-lease`), `git merge`, `git rebase`, `git reset --hard`/`--keep`, `git checkout -- <path>` / `git restore` that discards work, `git clean -fd`, `git branch -D`/`-d`, `git stash drop`/`clear`, `git tag -d`, `git worktree remove`, and any history-rewriting command.
- Read-only git commands are allowed (`status`, `diff`, `log`, `show`, `branch -v`, `remote -v`, `worktree list`, etc.).
- `git add` and `git stash push` are allowed only as setup for a commit/stash the user has just approved in this turn.
- Even when explicitly asked, the agent must (a) show the exact command(s) and full proposed commit message in chat, then (b) wait for a clear go-ahead before executing. A general "yes go ahead and work on this" earlier in the session does **not** count.
- Never bypass hooks (`--no-verify`, `--no-gpg-sign`) or update `git config` unless the user asks for it explicitly in the same turn.
- If the user says "commit and push", treat that as approval for both, but still preview the message first.

### Cloud Agent Exception

- Cloud Agents (Cursor agents running autonomously in the background cloud environment, not the interactive
  in-IDE assistant) are permitted to `git add`, `git commit`, and `git push` **only** on their own
  `cursor/*` working branch and **only** in service of creating or updating a pull request via the provided
  PR tooling. This PR create/update flow is the **single** sanctioned automated git-mutation path; no per-turn
  approval preview is required for it.
- Everything else in this Git Safety section still binds Cloud Agents: no history-rewriting or destructive
  commands (`--amend`, `--force`/`--force-with-lease`, `merge`, `rebase`, `reset --hard`/`--keep`, `clean -fd`,
  `branch -D`, `restore`/`checkout -- <path>` that discards work, etc.), no merging or enabling auto-merge, no
  bypassing hooks, and no leaving the working branch.
- Interactive (non-cloud) agents are unaffected by this exception and must follow the approval rules above.

## Commit And Pull Request Style (when the user has approved a commit/PR)

- Commit messages: imperative mood, ≤72 characters on the subject, reference tickets when applicable.
- PR checklist: concise summary, bullet list of changes, screenshots or HAR diffs for UI/network tweaks, and explicit test notes (`bun run test`, device steps, etc.).
- Link supporting docs or plans (e.g., `/plans/2026-05/0010-example-feature.md`) so reviewers can trace scope.
