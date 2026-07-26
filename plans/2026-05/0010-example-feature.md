# 0010 — Add a dark-mode toggle to the Settings screen

<!--
DEMO DEMO DEMO — created by AI to demonstrate the plan format described in
agents/commands/cmd_plan.md. The file paths and code in this plan are
fictional; do not implement it.

Numbered 0010 to demonstrate skip-by-10 standalone plan slots (see cmd_plan.md).
New projects use Ui* primitives; older GradeCorner code used a GC* prefix.
-->

## Description

Add a user-controlled dark-mode toggle to the Settings screen. The toggle overrides the OS color scheme and persists across launches. Today the app always tracks `useColorScheme()` from React Native, so users on a light-mode OS have no way to opt into the app's dark theme.

## References

- `src/screens/SettingsScreen.tsx` — `SettingsScreen` [verified]
- `src/uiElements/UiSwitch.tsx` — `UiSwitch` [verified]
- `src/data/store/themeStore.ts` — `useThemeStore` [assumed]
- `src/theme/ThemeProvider.tsx` — `ThemeProvider`, `resolveColorScheme` [assumed]
- `src/locales/en/settings.json` — strings for the new toggle row [verified]

## Reuse Audit

- `UiSwitch` already covers the row-with-toggle pattern used elsewhere in `SettingsScreen.tsx` (notifications row, sync-on-cellular row). No new component needed.
- Persistence reuses the existing `useThemeStore` Zustand slice — adding a `userOverride: 'light' | 'dark' | 'system'` field rather than creating a new store.
- No new utility functions. `resolveColorScheme(systemScheme, userOverride)` is the only new function and lives next to `ThemeProvider`.

## Algorithm

1. Extend `useThemeStore` with `userOverride: 'light' | 'dark' | 'system'` (default `'system'`) and a `setUserOverride` action that persists via the existing storage middleware.
2. In `ThemeProvider`, replace the direct `useColorScheme()` consumer with `resolveColorScheme(useColorScheme(), useThemeStore(s => s.userOverride))`. The resolver returns `userOverride` when it is `'light'` or `'dark'`, otherwise the system scheme.
3. In `SettingsScreen`, add a new row using `UiSwitch` bound to `userOverride === 'dark'`. `onValueChange` calls `setUserOverride(next ? 'dark' : 'light')`. (Tri-state "system" is reachable via a long-press reset, deferred — see Open Questions.)
4. Add the new locale string (`settings.darkMode.label`) to every file under `src/locales/*/settings.json`.

## Decision Points

- **Boolean toggle vs. tri-state selector**: chose a boolean toggle (light / dark) over a tri-state (light / dark / system) because every existing Settings row in this screen is a boolean `UiSwitch`. A tri-state would require introducing `UiSegmentedControl` here and breaks the visual rhythm of the screen. "Follow system" is reachable via a separate gesture if users ask.

## Confidence and Open Questions

- **High confidence** — `SettingsScreen.tsx` and `UiSwitch.tsx` were read; the row pattern and prop shape are confirmed.
- **Medium confidence** — `useThemeStore` and `ThemeProvider` are referenced by name from grep results but not opened end-to-end. The persistence middleware on `useThemeStore` is assumed to match the other Zustand stores in the project.
- **Open questions** — Should the "reset to follow system" gesture ship in v1, or land as a follow-up? Default behavior in this plan defers it.
