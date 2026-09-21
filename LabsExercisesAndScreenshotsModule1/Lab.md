# Module 1 - Prompting for UI Based on a Visual Contract



## Lab Guide

This guide contains the Lab walkthroughs and the activity instructions for Module 1. Follow each lab task alongside the instructor in VS Code. Refer to this document during the session whenever you need to revisit a prompt, a tab list, or a check.

> *LLM outputs are non-deterministic - your outputs may differ from what the instructor or this guide shows. Treat the references here as expected behavior, not as exact matches.*

> **Note for learners**: Each module starts from a known-good state of the app rather than from the previous module's end state. This is intentional: it means a module is never blocked by an earlier one, and you can join any module cleanly.

This Lab uses the branch `module-1-start`

---

## Module 1 Lab 1 - The vague UI prompt

**Goal:** Run a deliberately vague UI prompt and observe what Copilot has to guess when no visual contract is given.

### Setup

- Use `packages/frontend` for this demo
- The goal is to observe what Copilot guesses when useful context is removed

### Tab hygiene

Open only:

- `TaskInput.tsx` (active)

Close every other file. This is intentional - we are removing helpful app context so that the gaps in a vague prompt become visible.

### Prompt

In Copilot Chat, run:

```text
Create a To-Do app UI with:
- input field
- task list
- checkbox
- delete button

Use React, TypeScript, and Tailwind CSS.
```

- Do not add layout rules, component boundaries, or behavior details
- Let Copilot decide what the app should look like

### What to look out for in the output

The output can look usable, but Copilot fills in several decisions on its own based on the vague prompt:

- **Spacing**: padding and gaps between elements
- **Colors** : overall palettes, hues 
- **Layout**: page and element alignment
- **Component structure**: number of files, boundary placement
- **Interaction behavior**: actions when selecting add, complete, delete

These assumptions, while potentially reasonable, are not the same as design decisions.

### Quick contrast - valid code can still fail the task

After the first run, try this smaller follow-up prompt:

```text
Create a TaskCard component with title, assignee, and status badge.
```

- Copilot may create a valid component file
- The output in the browser may show no change, because the prompt does not specify integration
- The prompt never said where the component should be rendered

This is the integration gap - valid code is not automatically useful code.

### Copilot’s guesses based on a vague prompt

| Missing detail       | What Copilot had to guess                 |
| -------------------- | ----------------------------------------- |
| Layout rules         | Where elements should sit                 |
| Design system        | Which colors and spacing to use           |
| Component boundaries | Which parts become components             |
| Behavior             | How add, complete, and delete should work |
| Integration          | Where new code appears in the app         |

The next step is not a longer prompt, but rather a more constrained prompt.

---

## Module 1 Lab 2 - The constrained UI prompt

**Goal:** Run a constrained prompt that names layout, component boundaries, behavior, and styling so Copilot has fewer decisions to make.

### Tab hygiene

Open the files Copilot needs to understand the app:

- `TaskInput.tsx`
- `types/task.ts`
- `App.tsx`

Keep unrelated files closed. The goal is to give Copilot enough signal to follow structure and behavior.

### Prompt

In Copilot Chat, run:

```text
Create a desktop To-Do app using React + TypeScript + Tailwind CSS.

Acceptance Criteria:
- Centered container (max-width: 600px)
- Input height: 48px, full width
- Task items spaced vertically by 16px
- Each task row: checkbox, text, delete button aligned horizontally
- Completed tasks: line-through + reduced opacity

Component Boundaries:
- TaskInput: handles input and submit only
- TaskList: receives tasks as props, no state
- TaskItem: presentational component
- App: manages state using useState only

Behavior:
- Add task on Enter key
- Toggle complete state
- Delete task
- No API calls

Styling:
- Tailwind CSS only
- 8px spacing system
- Rounded corners (8px)
- Subtle shadow on container
- Delete button visible on hover

Output:
- Provide complete React + TypeScript code
- Keep code clean and readable
```

Notice how the prompt separates requirements into clear sections: acceptance criteria, component boundaries, behavior, styling, and output expectations.

### Expected result

- The layout reflects human decisions
- The app has a clearer input, list, and task item flow
- Add and complete behavior are easier to verify
- Local state is used
- No API calls are added

The output is still not visually final, but the behavior and structure are easier to evaluate because the prompt defined what success looked like.

---

## Exercise - Task 1: From vague to constrained - app header

**Goal:** Rewrite a vague UI prompt as a constrained prompt for the Methodical Tasks app header. This is a prompting exercise, not a React test.

### The initial vague prompt, to be rewritten:

```text
Create the app header with navigation.

Use React, TypeScript, and Tailwind CSS
```

### Before rewriting, consider these three questions:

1. What would Copilot have to guess from this prompt?
2. Which guess would matter most if it is wrong?
3. What constraint would remove that guess?

Feel free to jot notes down or share ideas in the meeting chat, but this is not required.

### Constructing the rewritten prompt:

Using the table below as a guide, <b>re-write the vague prompt</b> in the Copilot chat. 

| Prompt area     | Include                                                  |
| --------------- | -------------------------------------------------------- |
| Layout          | Sticky header, app name, tabs                            |
| Tab behavior    | Active and inactive states                               |
| Props           | Active view and change handler                           |
| Constraints     | No local state inside Header or TabNavigation            |
| File boundaries | Header and TabNavigation stay separate                   |


### Tab hygiene for running the revised prompt

- Keep `Header.tsx`, `TabNavigation.tsx`, and the relevant `App.tsx` usage open if needed
- Close unrelated components

### After running the constrained prompt

- Compare the output to the wireframe / reference design
- Note which elements changed and which ones did not

### Reference solution

The reference answers can be found in `Exercise.md`.

---

## Module 1 Lab 3 - Screenshot as a visual contract

**Goal:** Use a screenshot as the visual source of truth in place of description-based prompting.

### Setup

- Download the target UI screenshot in Copilot Chat (the completed Methodical Tasks view)
- This screenshot becomes the visual contract for the prompt

### Tab hygiene

Open only:

- `TaskInput.tsx`
- `TaskList.tsx`
- `types/task.ts`

Close unrelated files.

### Prompt

In Copilot Chat, attach the screenshot and use this prompt:

```text
I am sharing a **screenshot** of the target UI. Analyze it and recreate it as working React + TypeScript.

Requirements:

- Functional components with TypeScript
- Tailwind CSS for styling
- Match layout, spacing, and hierarchy visible in the screenshot as closely as practical
- Sensible component structure; reuse subcomponents where patterns repeat
- Responsive behavior only where the screenshot implies it

Focus on:

- Typography (sizes, weights, spacing)
- Colors and backgrounds
- Spacing (margin, padding, alignment)
- Interactive controls (buttons, inputs, focus states if visible)

Output:

- Complete component code and any subcomponents
- Placeholder copy/images where the screenshot shows media you do not have
```

This prompt gives Copilot a visual source, but it intentionally leaves some details open so the limits of screenshot-only prompting become visible.

### Expected result

- The layout is closer to the target UI than the constrained text-only prompt
- Header, hero text, input, and empty state align better
- Copilot still guesses some details - behavior, exact color values, or component boundaries

Screenshot reduces ambiguity, but does not eliminate it.

### Tighter version (second pass)

A stricter screenshot prompt defines what *matching* means, tells Copilot how to handle ambiguity, limits unrelated file edits, and preserves component boundaries and TypeScript contracts. The visible change between passes may be small - the important improvement is reliability.

### The tighter screenshot prompt: added context

```text
I am attaching a **screenshot** that is the visual source of truth. Generate React + TypeScript + Tailwind to match it.

Goal:
Recreate the UI so a side-by-side comparison with the screenshot shows **no intentional layout drift**.

Design constraints (from what is visible in the image):
- Match column widths, alignment, and vertical rhythm shown in the screenshot
- Match colors using exact-looking Tailwind classes or arbitrary values **only** where you can justify them from pixels in the image (state how you sampled them, e.g. browser eyedropper / design handoff)
- If the screenshot is ambiguous (cropped, low resolution), **state assumptions explicitly** in comments instead of inventing details

Component structure:
- Break into TaskInput, TaskItem, TaskList (or names that match what is shown)
- Do not duplicate code
- Keep components minimal and reusable

TypeScript:
- Explicit prop interfaces
- Do not use `any`

Styling:
- Tailwind CSS
- Prefer mapping repeated values to small local constants or a minimal theme object when the same numbers repeat

Behavior:
- No API calls unless the screenshot shows integration points and you are told to stub them

Output:
- Complete working code
- Prioritize visual fidelity over abstraction
```

---

## Exercise - Task 2: Identify missing components in a screenshot prompt

**Goal:** This is an analysis exercise. Do not run any prompts. Read the screenshot prompt from Demo 3 and identify what the prompt still needs to define, even with a screenshot attached.

### What to compare

- The screenshot prompt from Demo 3 (above)
- A tighter version of the same prompt above that adds review criteria, ambiguity handling, and scope limits

### Answer these questions

- In your notes, jot down responses to the following questions.
- Then, choose one question and share your response to that question in the chat.

1. The screenshot shows layout and structure - but what about **behavior** (hover, focus, click, expand) is not specified by an image alone?
2. The prompt asks Copilot to "match closely" - what does *matching* actually mean? Pixel-perfect, or approximate?
3. The prompt does not name **scope limits**. What might Copilot change that the prompt never asked it to change?
4. The screenshot may be cropped or ambiguous in places. What should the prompt tell Copilot to do when it is unsure - invent, or state an assumption?

### Focus areas

| Missing area         | Points to ponder                                           |
| -------------------- | ---------------------------------------------------------- |
| Match standard       | Is approximate enough, or should it be pixel-close?        |
| Component boundaries | Which parts should stay separate?                          |
| Component contract   | What props and data shape are expected?                    |
| Interaction rules    | What happens on hover, focus, click, or expand?            |
| Scope limits         | What should the model avoid changing?                      |
| Ambiguity handling   | What assumptions should be stated instead of invented?     |

A screenshot shows structure, but the prompt still defines the rules.

### Reference solution

The reference answers can be found in `Exercise.md`.

The next Lab begins on the branch `module-2-start`
