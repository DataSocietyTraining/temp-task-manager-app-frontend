# Module 1 - Prompting for UI Based on a Visual Contract

## Exercise Solutions

This document contains the reference answers for the two activity tasks in Module 1. Try the tasks first using `Lab.md` before reading these solutions.

---

## Task 1 solution - Strong constrained header prompt

A strong rewrite of the vague "Create the app header with navigation" prompt looks like this:

```text
Create a sticky app header using React + TypeScript + Tailwind CSS for the Methodical Tasks app.

Layout:
- Stays fixed at the top as the user scrolls
- App name “Methodical Tasks” on the left in purple, bold text
- Three navigation tabs in the center: Tasks, Focus, Archive

Tab behavior:
- The active tab has purple text and a purple underline
- Inactive tabs use muted purple text with no underline
- Clicking a tab calls onChangeView with the selected tab name

Props:
- currentView: the active tab name
- onChangeView: function called when the tab changes

Constraints:
- Header and TabNavigation should not manage local state
- App may own currentView state and pass it as props
- Do not add API calls
- Keep Header and TabNavigation as separate components
- Use explicit TypeScript props
- Do not use any

Output:
- Update Header and TabNavigation as separate presentational components
- Keep Header and TabNavigation driven only by props
```

### Why each section matters

| Section          | What it protects                                                                |
| ---------------- | ------------------------------------------------------------------------------- |
| Layout           | Removes guessing about position, alignment, and the visible app-name treatment  |
| Tab behavior     | Names active and inactive states so the model does not invent a visual style    |
| Props            | Gives the component contract - what data flows in, what callback flows out      |
| Constraints      | Stops local state from leaking into presentational components                   |
| File boundaries  | Keeps Header and TabNavigation as separate components, not merged into one      |
| Output           | Names what should change and what should remain prop-driven                     |

### The constraint that matters most

The prop contract - `currentView` in, `onChangeView` out - is what stops Header and TabNavigation from inventing local state. Without this line, Copilot often adds `useState` inside the header, which silently breaks the data flow between App and the rest of the views. Constraints like this are not pedantic; they prevent quiet structural drift.

### Troubleshooting - when the UI does not change

After running the prompt, the browser may show no visible change even though Copilot updated `Header.tsx` and `TabNavigation.tsx`. This usually means the components were created but not wired into `App.tsx`. The prompt specified *what to build*, but not *where to use it*.

A scoped integration prompt fixes this:

```text
The Header and TabNavigation components have been created, but the header is not visible in the running app.

Update App.tsx only enough to render the Header component at the top of the app.

Requirements:
- Import Header from ./components
- Add currentView state with these values: Tasks, Focus, Archive
- Pass currentView and onChangeView props to Header
- Render Header above the existing To-Do app content
- Keep the existing task input, task list, add, toggle, and delete behavior unchanged
- Do not rewrite Header.tsx or TabNavigation.tsx
- Do not add API calls
- Do not rename existing task functions or state variables

Output:
- Update App.tsx only
- The browser should show the Methodical Tasks header with Tasks, Focus, and Archive tabs
```

The pattern is: **build prompt → inspect output → add missing integration**. Frontend prompts often need both implementation instructions and integration instructions.

---

## Task 2 solution - What the screenshot prompt is not saying

The screenshot from Lab 3 carries layout, hierarchy, spacing, and color relationships. It does not carry behavior, contracts, or scope limits. A stronger prompt fills in what the image cannot.

### Answers to the four questions

**1. What about behavior is not specified by an image alone?**

A screenshot is static. It cannot show hover states, focus rings, click behavior, expand or collapse animations, keyboard interactions, or what happens on submit. The prompt must name these explicitly.

**2. What does "match closely" actually mean?**

The original prompt asks the model to match "as closely as practical" - this is ambiguous. A stronger prompt defines matching: side-by-side comparison shows no intentional layout drift, colors are sampled from the image (not invented), spacing is taken from the visible rhythm. Without a clear standard, Copilot decides what *close enough* means.

**3. What might Copilot change that the prompt never asked for?**

Without a scope limit, Copilot may edit `App.tsx`, change route handling, add new files, or rewrite the type definitions while it is "matching" the screenshot. A scope-guard line - "Only update these files" - prevents this.

**4. What should the prompt tell Copilot to do when the screenshot is ambiguous?**

The prompt should require Copilot to **state assumptions in code comments** rather than invent details silently. This converts a hidden guess into a visible one, which is reviewable.


### What changed between the two prompts

| Area                  | Original screenshot prompt          | Tighter screenshot prompt                                  |
| --------------------- | ----------------------------------- | ---------------------------------------------------------- |
| Match standard        | "as closely as practical"           | "no intentional layout drift" + sampled colors             |
| Component boundaries  | "sensible structure"                | Named components, no duplication                           |
| Ambiguity handling    | Not mentioned                       | State assumptions in comments, do not invent               |
| TypeScript discipline | Not mentioned                       | Explicit prop interfaces, no `any`                         |
| Behavior              | Implied from screenshot             | Explicit - no API calls unless shown                       |
| Scope limits          | Not mentioned                       | Output limited to working code, fidelity over abstraction  |

### The takeaway

A screenshot is useful evidence, but it does not automatically define component boundaries, behavior, prop contracts, or scope limits. Even with a strong visual reference, the prompt still has to do the work of defining the rules. A screenshot is one part of a visual contract - the prompt completes it.
