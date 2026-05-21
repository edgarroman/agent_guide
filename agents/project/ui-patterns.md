# UI Patterns and Theming

## Choosing a Component

When you need a component, work through this table top to bottom and stop at the first row that matches:

| Question                                                                   | → Use                                                                                  |
| -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| Does `src/uiElements/` already export something for this?                  | The existing uiElement (e.g. `GCView`, `GCText`, `GCAppHeader`, `GCSafeAreaView`)      |
| Is this a primitive that needs a theme? (text, container, button, surface) | A new wrapper in `src/uiElements/` over the RN Paper equivalent                        |
| None of the above?                                                         | Raw React Native, kept local to the screen                                             |

**Never import `react-native-paper` outside `src/uiElements/`.** All Paper components must be wrapped in a uiElement first — this keeps theming, accessibility defaults, and Paper version upgrades centralized in one folder. If you need a Paper component that isn't wrapped yet, add the wrapper in `src/uiElements/` (or extend an existing one) before consuming it. Most existing primitives (`GCText`, `GCButton`, `GCCard`, `GCDialog`, `GCSwitch`, `GCSegmentedControl`, `GCTextInput`, `GCCheckbox`, `GCAccordion`, `GCSheet`, `GCLoadingSpinner`, `GCSyncStatus`) are already Paper wrappers.

**Never** use raw `View` or `Text` for theme-sensitive UI — use `GCView` and `GCText`. For text specifically, always pick a `GCText variant=...` from `/docs/Typography.md` rather than passing raw size/weight props. Mixing component libraries in a single file is a smell; pick one layer per file.

`src/uiElements/` has no barrel `index.ts`, and one should not be added. Import each component by its file path (`@/uiElements/GCButton`, `@/uiElements/layout/GCView`, `@/uiElements/layout/GCSafeAreaView`).

## Choosing How to Style

| Question                                                                       | → Use                                                 |
| ------------------------------------------------------------------------------ | ----------------------------------------------------- |
| Does the underlying uiElement accept a prop for this (variant, tone, spacing)? | The prop                                              |
| Is the style used once in this file?                                           | An inline style object on the JSX                     |
| Is the style repeated in this file?                                            | A `StyleSheet.create` block at the bottom of the file |
| Is the style repeated across files?                                            | Lift it into the uiElement that owns the pattern      |

Use Flexbox for layout and `useWindowDimensions` for screen-size-dependent values. Prefer responsive layout over breakpoint branching.

## Choosing a Safe Area API

| Question                                          | → Use                                                                                                                  |
| ------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| Top-level layout / screen wrapper?                | A safe-area-aware uiElement (`GCAppHeader`, or `GCSafeAreaView` from `@/uiElements/layout/GCSafeAreaView`)              |
| Need to opt out of one inset on a screen wrapper? | `GCSafeAreaView` with `disableTop` / `disableBottom` / `disableLeft` / `disableRight` props                            |
| Need specific inset values for custom spacing?    | `useSafeAreaInsets` / `useSafeAreaFrame` from `react-native-safe-area-context`                                         |
| Scrollable content near a notch/home indicator?   | A safe-area-aware wrapper, or `contentInsetAdjustmentBehavior` on the ScrollView                                       |

Wrap the app in `SafeAreaProvider` once at the root. Do **not** import `SafeAreaView` from `react-native` core — it was deprecated in SDK 54.

## Dark Mode and Class Palette

- Implement dark mode support using Expo's `useColorScheme` and React Native Paper's theming system
- Ensure all custom components support both light and dark themes; test in both
- For per-class accent colors, use `getClassColor` / `getOnAccentColor` from the class-palette system documented in `/docs/ThemePaletteSystem.md`. Do not hand-roll color assignments per class

## Accessibility

- Ensure high accessibility (a11y) standards using ARIA roles and native accessibility props
- Provide semantic meaning for UI elements
- Support screen readers and keyboard navigation
- Test accessibility features regularly

## Animation and Gestures

- Use react-native-reanimated and react-native-gesture-handler for performant animations and gestures
- Implement smooth transitions between states
- Use native animations when possible for better performance

## Performance Optimization

- Use Expo's `SplashScreen` (or the managed App Loading flow) to keep startup responsive
- Prefer `expo-image` with WebP assets, defined dimensions, and lazy loading for media-heavy views
- Split non-critical UI into lazily loaded chunks with `React.lazy`/`Suspense` when it reduces initial render cost
- Profile components with Expo and React Native dev tools; memoize or use `useMemo`/`useCallback` to avoid unnecessary re-renders

## Screen Structure

- Follow standardized patterns documented in `/docs/TypicalScreenArchitecture.md`
- Use consistent layout patterns across screens
- Implement proper loading and error states
- Follow established navigation patterns
