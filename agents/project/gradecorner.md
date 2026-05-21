# GradeCorner Project Guidelines

## Project Architecture

- This is a local-first app where much of the data is stored in SQLite using a service pattern
- The service pattern translates database objects to TypeScript objects (raw → domain → mapper). See `/docs/DataAccess.md`
- Sync from gradebook → SQLite is described in `/docs/GradebookSync.md` (resource-centric `defineSyncResource` / `runSyncJob` / journal)
- `/docs/archive/` is non-authoritative — quarantined for human reference only; do not apply its patterns to feature work

## Folder Structure

- Use expo-router for all routes under `/src/app`
- Route files should be stubs that load actual screens from `/src/screens`
- Organize code by feature areas and maintain clear separation of concerns

## Navigation and Linking

- Use expo-router + React Navigation patterns for stacks and tabs. The shell layout (top stack + main-app tabs + Command Sheet) is documented in `/docs/AppNavigation.md`
- Configure deep linking and universal links to keep navigation in sync across platforms
- Prefer dynamic routes in expo-router when a screen maps to parameterised data
- Keep navigation side effects (refresh, redirects) close to route files while delegating UI to screens

## UI and Styling

See `agents/project/ui-patterns.md` for the full decision tables. Quick rules:

- Use components in `src/uiElements/` for common UI patterns and layouts; deep-import them (no barrel)
- Avoid using `View` and `Text` directly — use `GCView` and `GCText` which are theme-aware
- Never import `react-native-paper` outside `src/uiElements/`. If you need a Paper component that isn't wrapped, add the wrapper in `src/uiElements/` first
- Handle safe areas via `GCSafeAreaView` or `GCAppHeader` from `src/uiElements/`. Never import `SafeAreaView` from core `react-native` (deprecated in SDK 54); when you need raw inset values, pull them from `useSafeAreaInsets()` in `react-native-safe-area-context`

## State Management

When you need to store or read state, work through this table top to bottom:

| Question                                                                        | → Use                                                                                                                       |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Is this data that came from (or syncs to) the gradebook server?                 | TanStack React Query hook under `src/data/hooks/`. The `queryFn` reads from SQLite (populated by sync code via services)    |
| Is this durable user data owned by the app (settings, drafts, cached entities)? | Drizzle ORM over expo-sqlite, accessed through a service in `src/data/db/*Service.ts`                                       |
| Is this UI-only state (open/closed, focused tab, form input being edited)?      | Local component state (`useState`; rarely `useReducer` for complex calculator/form state — see `GradeCalculatorContext.tsx`) |
| Is this UI-only state needed by a few sibling components?                       | Lift to the nearest common parent, then pass via props or `useContext`                                                      |

Services in `src/data/db/*Service.ts` own the translation between Drizzle rows and TypeScript objects (e.g. `toUser` in `userService.ts`, `mapClassRecord` in `classService.ts`). React Query hooks in `src/data/hooks/` wrap these services; components consume the hooks, not raw query results. The React Query cache is in-memory only — there is no `persistQueryClient`; SQLite is the durable layer. See `/docs/DataAccess.md` for the raw-vs-domain typing pattern and `defineLocalLoadProfile`.

Do not introduce additional global state libraries (Redux, MobX, Jotai, Recoil). The existing `zustand` stores under `src/data/store/` are reserved for narrow app-wide concerns; new Zustand stores require justification — prefer React Query, context, or local state first.

For one-shot cross-component notifications (SSO completed, network status flipped, etc.) use the typed Emittery bus documented in `/docs/EventSystem.md`. Do not invent ad-hoc pub/sub patterns alongside it.

## Localization

- Use i18next for all translations
- All strings displayed to users should use `/src/utils/i18nUtils.ts`
- Insert strings into all locale files in `/src/locales`
- Never hardcode user-facing strings
- Do not introduce alternate i18n libraries (e.g., `react-native-i18n`, `expo-localization`)—they are superseded by i18next

## Gradebook Systems

- Gradebook systems are abstracted in `/src/gradeBookSystems`. See `src/gradeBookSystems/README.md` for the integration interface and the per-system entry points
- Adding a new system should not impact core code
- Use defined interfaces for gradebook integration; sync flows go through `defineSyncResource` (see `/docs/GradebookSync.md`)
- Network calls go through the two-layer stack documented in `/docs/NetworkFramework.md` (`httpClient` + gradebook session client)

## Specs and Plans

- For significant tasks or when asked for a spec, use `/agents/commands/cmd_plan.md` and store it under `/plans/YYYY-MM/`

## Development Workflow

- Follow established patterns in existing code
- Use TypeScript strictly - avoid `any` type
- Implement proper error handling and validation
- Write tests for critical functionality
- Consider accessibility in UI implementation
- Capture runtime errors via `src/logging/sentry.ts` (`@sentry/react-native`). Use the `Log` helpers from `src/logging/` rather than `console.*`. See `/docs/LoggingGuide.md` for the `Log`/`LogSubSys` API and sampling rules

## Code Formatting

- Completed code should have no lint errors, no unused variables
- Imports are sorted by the TypeScript Language Service ("Organize Imports" in VS Code / `tsserver`): unused imports removed, modules sorted alphabetically (case-insensitive), named bindings sorted within braces. `yarn lint` enforces this

## Expo Workflow and Platform Conventions

- Stay within Expo's managed workflow unless a requirement forces ejecting
- Prioritise Mobile Web Vitals: fast load time, low jank, responsive interactions
- Use `expo-constants` for environment configuration and device metadata
- Request permissions via the owning Expo module (`Notifications.requestPermissionsAsync()`, `ImagePicker.requestMediaLibraryPermissionsAsync()`, `LocalAuthentication.*`, etc.). The standalone `expo-permissions` package was removed and is not installed
- Validate behaviour on both iOS and Android during feature work
