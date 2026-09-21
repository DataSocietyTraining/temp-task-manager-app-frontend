# Module 2 - Figma MCP and Variant Prompting



## Lab Guide

This guide contains the demo walkthroughs and the activity instructions for Module 2. Follow each demo alongside the instructor in VS Code. Refer to this document during the session whenever you need to revisit a prompt, a tab list, or a validation check.

> LLM outputs are non-deterministic - your outputs may differ from what the instructor or this guide shows. Treat the references here as expected behavior, not as exact matches.

This Lab uses the branch `module-2-start`

---

## Module 2 Lab 1 - Connect Figma MCP and extract design context

**Goal:** Verify that Copilot can retrieve structured design values from a Figma frame through MCP.

### Setup

1. Open Command Palette:
   - `Cmd + Shift + P` (Mac)
   - `Ctrl + Shift + P` (Windows)
2. Run `MCP: Open User Configuration`.
3. Paste this into `mcp.json` and save:

```json
{
  "inputs": [],
  "servers": {
    "figma": {
      "url": "https://mcp.figma.com/mcp",
      "type": "http"
    }
  }
}
```

4. Click `Start` above the `figma` server entry.
5. Complete Figma OAuth (authentication) in the browser.

### Branch baseline

Create a clean working branch from the provided baseline:

```bash
git checkout -b module-2-start-attempt module-2-start
```

This keeps `module-2-start` untouched as a safe fallback and gives you a known baseline to restore to between demos.


### Prompt

In Copilot Chat (Agent mode), run:

```text
https://www.figma.com/design/FFPdwRiIoEa5NV3XupRlXV/Full-Stack-IQVIA-course?node-id=7-708&t=Q4sUeZxh1dO2Xopf-1

Get design context from this Figma frame
```

### Expected result

- Copilot calls Figma MCP using the frame link.
- The response includes structured design values.
- You can confirm MCP is working before any UI generation prompt.

### What to look out for

- If Start button does not appear, save `mcp.json` again and reload VS Code.
- If MCP is not called, confirm Agent mode is on and OAuth completed.

---

## Module 2 Lab 2 - Vague vs constrained Figma prompt

**Goal:** Compare how prompt specificity changes structure and scope when the same Figma source is used.

### Tab hygiene

Open only:

- `App.tsx`
- `TaskInput.tsx`
- `types/task.ts`

Close unrelated files.


### First pass prompt (vague)

```text
<FIGMA LINK>

Recreate the Figma design exactly in React + Tailwind CSS.
Match spacing, font sizes, and colors precisely.
```

### Expected first-pass behavior

- Visual output can look close because MCP supplies design values.
- Structure can drift because component boundaries are not constrained.
- Copilot may collapse implementation into fewer files.

### Reset before second pass

```bash
git restore packages/frontend/src/
```

### Second pass prompt (constrained)

```text
<FIGMA LINK>

Using the provided Figma design, generate React + TypeScript components using Tailwind CSS.

Goal: Recreate the UI with pixel-perfect accuracy based on the design source of truth.

Design constraints:
- Match layout, spacing, typography, and colors exactly from Figma
- Desktop frame: 1440px, container max-width: 1296px, 72px side gutters
- Follow exact spacing values - no approximation
- Use exact color values from the design

Extraction rules:
- If MCP is available: extract all values directly from the Figma file
- State assumptions explicitly in code comments

Component structure:
- Break into TaskInput, TaskItem, TaskList - no code duplication
- Keep components minimal and reusable

TypeScript: explicit prop interfaces, no any
Styling: Tailwind CSS only; prefer design tokens over hardcoded values
Behavior: no API calls; keep components presentational

Output: complete working React + TypeScript code. Visual fidelity over abstraction
```

### Expected behavior

- Visual output may still look similar to the vague pass.
- Structural quality improves: separate component files, clearer contracts, better traceability.
- Files modified should reflect the required component boundaries.

### Key takeaway

Same Figma source, different prompt discipline:

- Vague prompt: Copilot decides structure and scope.
- Constrained prompt: prompt decides structure and scope.

---

## Module 2 Demo 3 - Variant prompting with TaskItem states

**Goal:** Generate a single component that supports two design states through a prop.

### Tab hygiene

Open:

- `TaskItem.tsx`
- `TaskList.tsx`
- `App.tsx`
- `types/task.ts`

### Setup

Capture two TaskItem screenshots from Figma:

- Active task state
- Completed task state

### Prompt

Attach both screenshots (M5_Lab3_state1.png, M5_Lab3_state2.png) in Copilot Chat (Agent mode), then run:

```text
I am sharing screenshots of two UI states. Build components that implement both states.

Requirements:
- Organize shared layout in parent; keep state-specific pieces in children or conditional branches
- Match colors and spacing across screenshots so the two states feel like one system
- Use TypeScript props to model the varying state, e.g. variant or isEmpty

Design already solved the variants - reuse structure, do not reinvent it.
```

### Expected result

- A single `TaskItem` handles both visual states.
- State switch is prop-driven (for this module, typically `completed`).
- The prompt extends existing constrained structure rather than rebuilding from scratch.

### What to verify

- Active tasks: white card treatment.
- Completed tasks: muted treatment with completed styling.
- No duplicate components created for each state.

---

## Exercise - Task 1: Build the FocusModeCard variant prompt

**Goal:** Complete the construction of a prompt that generates one FocusModeCard component with two visual states: one that represents the card in a standard view, and one that changes the card into “Focus Mode.” 

### Scenario

- You want to design this application to include a FocusModeCard that appears when high-impact tasks exist for the user. 
- The card should have the same layout in both states.
- However, the button on the card should change based on which state is activated:
  - Standard (not in Focus view): `View Insights`
  - In Focus view: `Back to Tasks`

### Tab hygiene

Open only:

- `FocusModeCard.tsx`
- `App.tsx`
- `Button.tsx`

### Prompt scaffold

Fill the missing sections of this prompt before running:

```text
Build the FocusModeCard component with two
states - same layout, only the button changes.

When the user is not in focus view, the button
should say 'View Insights' and [BLANK 1].
When they are in focus view, it should say
'Back to Tasks' and [BLANK 2].

The card has a dark purple gradient background,
a bold white 'Focus Mode' title, and a subtitle
that reads [BLANK 3]. 

Use the existing Button component - don't create
a new one. One component, one return statement,
no duplicated layout.

Wire this into App.tsx for full navigation behavior so I can see how it works
```

### Guiding questions

1. What should each button click do in each state?
2. What is the exact subtitle string pattern?

### Verify after running

- Same card layout in both states.
- Button label and action change by `isInFocusView`.
- Existing shared `Button` component is reused.
- Navigation wiring works from Tasks to Focus and back.


### Reference solution

The reference answers can be found in `Exercise.md`.

The next Lab begins on the branch `module-3-start`
