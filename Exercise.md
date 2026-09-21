# Module 3 - Design Tokens and Performance Optimization

## Exercise Solutions

This document contains the reference answers for the two activity tasks in Module 3. Try the tasks first using `Lab.md` before reading these solutions.

---

## Task 1 solution - Strong constrained refactor prompt

A strong prompt for refactoring `Header.tsx` and `HeroSection.tsx` to consume `theme.ts` looks like this:

```text
Refactor Header.tsx and HeroSection.tsx to use values from theme.ts
instead of hardcoded color values.

One allowed goal: replace hardcoded values with theme token references.
Nothing else may change.

Constraints:
- The visual output must be identical - a side-by-side comparison shows no difference
- Do not rename any component, prop, or exported function
- Do not remove or change any conditional logic
- If a color in the component does not exist in theme.ts yet, add it to theme.ts first
- Only touch Header.tsx, HeroSection.tsx, and theme.ts - nothing else
```

### Why each line matters

| Line | What it protects |
| --- | --- |
| One allowed goal | Stops the model from mixing optimization or cleanup into the refactor |
| Visual output must be identical | Gives a verifiable post-run check |
| Do not rename anything | Stops silent prop or function-name drift |
| Do not change conditional logic | Keeps state-driven styles intact |
| Add to theme.ts if missing | Allows the theme to grow if needed, rather than the component approximating a value |
| Only touch these three files | The scope guard - the line that matters most |

### The constraint that matters most

The scope line - "Only touch Header.tsx, HeroSection.tsx, and theme.ts" - is what stops the model from "helpfully" refactoring `TaskItem.tsx` or `TaskInput.tsx`. Without it, the demo work from earlier in the module can be silently overwritten. Scope limits are not pedantic. They are what stop the model from doing more than was asked.

---

## Task 2 solution - Three problems in the flawed prompt

### Problem 1 - missing pair (the coupling)

The prompt asks to wrap `FocusModeCard` with `React.memo` but never asks to stabilize the callback props passed in from `App.tsx`. Memo on FocusModeCard does not help if those function references keep changing.

**Fix:** add a line like:

```text
- Also wrap onViewInsights and onBackToTasks in App.tsx with useCallback so their references stay stable across renders.
```

This is the most important problem. The prompt looks complete on paper. Code review passes - FocusModeCard is memoized, comments are in place. But the component can still re-render because callback references change on each parent render, so `React.memo`'s comparison fails.

### Problem 2 - unnecessary memoization

The prompt asks to memoize `buttonLabel` and `buttonHandler` with `useMemo`, but both are trivial ternary expressions. Memoizing them adds dependency-tracking and comparison overhead without measurable savings.

**Fix:** remove that bullet from the prompt:

```text
- Wrap buttonLabel with useMemo - dependency is isInFocusView   <- delete this line
- Wrap buttonHandler with useMemo - dependency is isInFocusView, onViewInsights, onBackToTasks   <- delete this line
```

### Problem 3 - missing scope guard

The constraints say "do not change the props interface of FocusModeCard" but nothing stops the model from adding memoization beyond what was asked.

**Fix:** add this line to the constraints:

```text
- Only apply the explicitly requested optimization; do not memoize additional values, handlers, or nearby components.
```

Without this, the model may add extra memoization inside `FocusModeCard` (or in nearby components) that was never requested, increasing complexity with little or no runtime benefit.

### The takeaway

Problem 1 is the most important - it creates **false confidence**. The optimization looks correct in code review, but at runtime re-renders still happen because callback identities are unstable. A constrained prompt is what forces that coupling to be handled before the model writes a single line.
