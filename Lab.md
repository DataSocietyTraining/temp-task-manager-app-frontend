# Module 3 - Design Tokens and Performance Optimization

## Lab Guide

This guide contains the demo walkthroughs and the activity instructions for Module 3. Follow each demo alongside the instructor in VS Code. Refer to this document during the session whenever you need to revisit a prompt, a tab list, or a check.

> LLM outputs are non-deterministic - your outputs may differ from what the instructor or this guide shows. Treat the references here as expected behavior, not as exact matches.

This Lab uses the branch `module-3-start`

---

## Module 3 Lab 1 - Token extraction

**Goal:** Generate `theme.ts` from the hardcoded values in `TaskItem.tsx` and `TaskInput.tsx`.

### Setup

1. Create the empty theme file from the terminal:

   ```bash
   touch packages/frontend/src/theme.ts
   ```

2. Open `tailwind.config.js` and confirm it has no custom tokens yet. The theme should start empty with no carry-over from earlier modules.

### Tab hygiene

Open exactly these files in VS Code:

- `theme.ts` (blank, active)
- `TaskItem.tsx`
- `TaskInput.tsx`
- `tailwind.config.js`

Close every other file. With both components open, Copilot can see every hardcoded value it needs to extract.

### Prompt

With `theme.ts` active, run the following in Copilot Chat (Agent mode):

```text
From TaskItem.tsx and TaskInput.tsx, extract the following design tokens:
- Primary color (the purple used for interactive elements and the logo)
- Surface colors (card background, page background, muted/archived state)
- Accent colors (red for Add button, amber for star, green for completing state)
- Spacing values (base unit and common multiples visible in the components)
- Border radius values (cards, inputs, buttons, checkboxes)
- Typography scale (heading sizes, body size, label size, font weight variants)

Convert these into a TypeScript theme object in theme.ts:
- Use a const object with 'as const' and explicit type annotation
- Group by category: colors, spacing, radius, typography
- Use semantic names, not raw values ('primary' not 'purple1')
```

### Expected result

The generated `theme.ts` should have:

- A `const theme` object declared `as const`
- An exported `Theme` type
- Tokens grouped into `colors`, `spacing`, `radius`, `typography`
- File compiles cleanly

The theme is now the source of truth - components will read from it in Demo 2.

### What to look out for in the output

Even with a constrained prompt, the model often does things the prompt did not ask for:

- **Reads files beyond what was named** - it may open other source files on its own to gather context
- **Sometimes names accents by color, not by purpose** - e.g. `red` / `amber` / `green` instead of `danger` / `warning` / `success`
- **Pads the theme** - may add entries that no component actually uses
- **Fills in missing values** - makes a plausible guess when the source has gaps, like an undefined CSS variable

A constrained prompt cuts guessing down - it does not stop it completely.

### Discussion Question 

- Can you find any items in the output on your local machine that the model created but you did not ask for in this prompt?

---

## Module 3 Lab 2 - Constrained token refactor

**Goal:** Refactor `TaskItem.tsx` and `TaskInput.tsx` to consume values from `theme.ts`. Visual appearance must stay identical and `Header.tsx` and `HeroSection.tsx` must not be touched.

### Tab hygiene

**Keep open** these files in VS Code: 

- `TaskItem.tsx` 
- `TaskInput.tsx` 
- `theme.ts`

Close any other files so they stay outside the refactor's reach/

Make sure that `TaskItem.tsx’ is the active tab.

Save `theme.ts` before running the prompt - if it is incomplete, the model will import token names that have not been written yet, and the refactor will not compile.

### Prompt

```text
Refactor TaskItem.tsx and TaskInput.tsx to consume values from theme.ts
instead of hardcoded Tailwind arbitrary values (e.g. text-[#4c1d95], bg-[#f8fafa]).

Constraints:
- Do not change the visual output - side-by-side comparison should show no difference
- Do not rename any component, prop, or exported function
- Do not remove or weaken any conditional styling (completing, archived, editing states)
- The app should still compile cleanly and the existing interaction flow should still work
- Do not extract to new files beyond theme.ts
- Only touch TaskItem.tsx, TaskInput.tsx, and theme.ts
```

Each constraint maps to a check that can be made after the prompt runs.

### Expected result

- The components now read from `theme.ts`
- The UI in the browser looks and behaves exactly as before - no visible change in the frontend output
- `Header.tsx`, `HeroSection.tsx`, and `App.tsx` remain untouched

The "Only touch" line is the scope guard. Without it, the model often "helpfully" refactors adjacent components.

---

## Exercise - Task 1: Write the constrained refactor prompt

**Goal:** Write a prompt that refactors `Header.tsx` and `HeroSection.tsx` to use `theme.ts`. This is a prompting exercise - not a React exercise.

### The situation

- `theme.ts` already holds the design tokens from Lab 1
- `Header.tsx` and `HeroSection.tsx` still use hardcoded colors
- `TaskItem.tsx` and `TaskInput.tsx` were updated earlier in Lab 2 - **leave them as-is**

### Before writing the prompt, answer these three questions:

1. What is the one allowed goal of this refactor?
2. What three things must stay the same so the app still looks and runs the same?
3. What files may the prompt touch, and what must it leave alone?

### Tab hygiene before you run

**Open only**: 
- `Header.tsx` (active) 
- `HeroSection.tsx` (active), 
- `theme.ts` (must be fully saved before the prompt runs)

NOTE: If `TaskItem.tsx` or `TaskInput.tsx` are open here, the model may decide to "re-refactor" them, which would overwrite Demo 2's work. Closing them is part of the scope discipline.

### After running

Verify:

- The visual output is identical - open the app before and after; side-by-side should show no difference
- No hardcoded hex color values remain in `Header.tsx` or `HeroSection.tsx`
- `theme.ts` was updated if any color was missing - not silently approximated to a different color
- `TaskItem.tsx` and `TaskInput.tsx` were not touched - the constraint held
- No component or function was renamed

### Reference solution

The reference answers can be found in `Exercise.md`.


---

## Module 3 Lab 3 - Performance optimization

**Goal:** Add `React.memo`, `useCallback`, and `useMemo` to `App.tsx` and `TaskItem.tsx` only where listed without over-optimization.

### Tab hygiene

**Open**: 
- `App.tsx` (active), 
- `TaskItem.tsx`, 
- `TaskList.tsx`

Close every other tab. `App.tsx` is one of the largest files in this workflow, so keeping unrelated files closed helps preserve relevant context

Every unnecessary open tab adds noise and can pull Copilot's focus away from the active task (this is the Module 1 noise-filter lesson at its most concrete)

### Prompt

```text
Optimize App.tsx for rendering performance.

Goals: improve rendering efficiency, avoid unnecessary re-renders, keep code clean - no over-engineering.

Rendering optimization:
- Wrap TaskItem with React.memo in TaskItem.tsx - it should only re-render when its task prop changes
- Ensure each task uses task.id as its key - never array index

Event handlers (required for React.memo to actually work):
- Wrap handleToggleTask and handleDeleteTask with useCallback in App.tsx
- These are passed to TaskItem - stable references are required or React.memo's comparison always fails

Derived data:
- Wrap visibleTasks with useMemo - dependencies are tasks and currentView
- Wrap highImpactWaitingCount with useMemo - dependency is tasks only

Constraints:
- Do not change the props interface of any component
- Do not alter the visible output or any error/validation display
- Do not add memoization beyond what is listed above
- The app should still compile cleanly and the existing interaction flow should still work

Output: optimized code with a comment on each change explaining what it does and why.
```

### Expected result

- `App.tsx` now wraps the two handlers in `useCallback` and the derived values in `useMemo`
- `TaskItem.tsx` is wrapped in `React.memo`
- Each change has a one-line comment explaining what and why
- Nothing else in the app was touched

The app looks and behaves the same and work to render is reduced under the hood.

### Verify behavior in the browser

Confirm every behavior still works:

- Adding a task
- Toggling complete (the completing animation still shows)
- Star button toggles high-impact, and the count updates
- View Insights switches to Focus
- Back to Tasks returns
- Archive shows completed tasks
- Delete works

If `TaskInput`, `FocusModeCard`, `Header`, or `HeroSection` show up in the model's edited file list, that is over-optimization - the "do not add memoization beyond what is listed" constraint should have prevented it.



---

## Exercise - Task 2: Identify missing components in the optimization prompt

**Goal:** This is an analysis exercise. Do not run any prompts. Read a flawed optimization prompt and identify what is missing, what is unnecessary, and what constraint is too weak.

### Two principles to know before reading

- **Principle A:** You can tell a component to skip re-renders unless its data changes - but only if the functions you pass into it stay the same across renders. The two go together.
- **Principle B:** Memoization is not free. Adding it to something that already renders cheaply costs more than it saves.

### Read this flawed prompt

```text
Optimize the FocusModeCard component for rendering performance.

Rendering optimization:
- Wrap FocusModeCard with React.memo so it only re-renders when its props change

Derived data:
- Wrap buttonLabel with useMemo - dependency is isInFocusView
- Wrap buttonHandler with useMemo - dependency is isInFocusView, onViewInsights, onBackToTasks

Constraints:
- Do not change the props interface of FocusModeCard
- The app should still compile cleanly and the existing interaction flow should still work

Output: optimized files with comments explaining each change.
```

In this prompt, something is **missing**, something is **unnecessary**, and one constraint is **too weak**.

### Answer these three questions to identify the flawed components

1. The prompt says "wrap FocusModeCard with React.memo so it only re-renders when its props change." Look at how `onViewInsights` and `onBackToTasks` are passed in from `App.tsx`. Using **Principle A** - what is missing from this instruction that will make it fail to work? Write one sentence fixing it.

2. The prompt says "wrap buttonLabel and buttonHandler with useMemo." Look at how they are defined in the component - they are both a single ternary expression. Using **Principle B** - is memoizing them worth the cost? Write one sentence explaining why or why not.

3. The constraints say "do not change the props interface of FocusModeCard" - but nothing stops the model from memoizing whatever else it decides is worth it inside the component. What might the model add that the prompt never asked for? Write one sentence that would prevent it.

### Reference solution

The reference answers can be found in `Exercise.md`.

