# TypeScript Coding Standards

## Code Style and Structure

- Write concise, technical TypeScript code with accurate examples
- Use functional and declarative programming patterns; avoid classes unless encapsulation and state management is necessary
- Prefer iteration and modularization over code duplication
- Default exports are only permitted in `src/app/` route files (Expo Router requires it). Everywhere else — including `src/screens/` — use named exports. Route files do `export default FooScreen` (or `export default withRouteData(FooScreen, ...)`); the screen itself is `export const FooScreen = ...`
- Assume Strict Type-Checking mode is active. Warn if it is not active in tsconfig.json
- Watch for unhandled or floating Promises and avoid them with generated code and warn if found in existing code
- Enforce importing types using `import type` syntax and warn if found in existing code. When mixing values and types from the same module, prefer the inline form (`import { useState, type ReactNode } from 'react'`) over splitting into two import lines
- Avoid type assertions (`as SomeType`). If a value's type is genuinely unknown, narrow it with a type guard or schema validator (Zod). Permitted exceptions:
  - `as const` for literal narrowing
  - `as unknown` followed by a type guard or Zod parse
  - Branded type constructors inside a validator (e.g. `value as UUID` inside `assertUUID`)
  - `as P` inside generic HOCs where the generic can't otherwise be expressed (e.g. `withRouteData`)
- Remove any unused imports from all generated code
- Do not create new `index.ts` barrel files — they make tree shaking more difficult and expand the dependency graph unnecessarily. Import directly from the defining module instead (`import { Foo } from './foo'`, not `import { Foo } from './'`). A handful of legacy barrels still exist; do not add to them and do not create new ones

## Imports

- Try to locate generic project-wide types into a top level directory except for functional area types should be grouped in a functional area 'types' directory
- If tsconfig.json has paths (usually @ or ~) then use them for all imports outside the current directory hierarchy. Relative imports are ok for files in the same directory or in sub directories
- Never put significant code inside `index.ts`. If a file would otherwise be named `index.ts`, give it the name of its primary export (e.g. `userService.ts`, not `index.ts`) — this also makes editor tabs distinguishable when several files are open

## Naming Conventions

- Generally use camelCase for folders unless a framework encourages kebab-case (e.g. expo-router or next.js)
- Use descriptive variable names with auxiliary verbs (e.g., isLoading, hasError)

## TypeScript Usage

- Use TypeScript for all code; prefer interfaces over types
- Avoid enums; use literal unions instead or mapped types. Use 'as const' and then export type for ease of use and strong type checking
- Use functional components with TypeScript interfaces
- When defining functional components, prefer usage of the 'const' keyword and use fat arrow notation and ensure signature is fully typed including return codes
- Use fat arrow notation for all functional components
- Avoid using React.FC - this is obsolete. Type props via an explicit interface. When annotating the return type, use `ReactElement` (or `ReactElement | null`) imported from `react` — not `JSX.Element`, not `React.FC` (e.g. `const Foo = ({ id }: FooProps): ReactElement => …`)
- Avoid using React.Component - this is obsolete. Write functional components with hooks
- Never use the var keyword. Use `const` by default; use `let` only when reassignment is required
- Avoid the use of 'any' type. If necessary, use the 'unknown' type
- Generics should always use a type parameter
- Functions should never return 'any' - use 'void' if no return value is provided
- Optional parameters are ok to be used. They are ideal for extending existing interfaces / types to ensure backward compatibility

## Syntax and Formatting

- Use the "const" keyword for pure functions
- Avoid unnecessary curly braces in conditionals; use concise syntax for simple statements. For a single-statement `if`, write it inline (`if (!user) return null;`) rather than wrapping in braces on multiple lines. Exception: when the body is a multi-line JSX block, keep the braces
- Use declarative JSX
- Use Prettier for consistent code formatting

## Error Handling and Validation

- Prioritize error handling and edge cases:
  - Handle errors at the beginning of functions
  - Use early returns for error conditions to avoid deeply nested if statements
  - Avoid unnecessary else statements; use if-return pattern instead
  - Implement global error boundaries to catch and handle unexpected errors

## Hooks

- Always preserve hook order. Never conditionally call hooks like useXyz() based on props/state. Instead:
  - Call hooks unconditionally with inert defaults (e.g., '', null, or safe objects)
  - Gate side-effects and UI usage with real conditions

Example: call useSync(accountId || '') always; only refresh() and render when accountId is truthy.
