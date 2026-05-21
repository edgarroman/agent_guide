# Agent Rules

Coding standards and reusable prompts for AI agents working on this project.

| Directory    | Contents                                                  |
|--------------|-----------------------------------------------------------|
| `common/`    | Language-level rules: TypeScript, testing, security       |
| `project/`   | GradeCorner-specific: architecture, UI patterns           |
| `commands/`  | Reusable prompts: planning, code review, documentation    |

`.cursor/rules/*.mdc` files use globs to load the right rules for the files being edited. See `AGENTS.md` at the repo root for the full project overview.

Pull request descriptions use `.github/PULL_REQUEST_TEMPLATE.md` so GitHub and automation can discover the same template. Keep PR template refinements there.
