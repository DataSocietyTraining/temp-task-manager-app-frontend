# Module 2 - Figma MCP and Variant Prompting

## Exercise Solutions

This document contains the reference answer for the activity task in Module 2. Try the task first using `Lab.md` before reading this solution.

---

## Task 1 solution - FocusModeCard variant prompt

A strong filled-in prompt looks like this:

Attach both screenshots (M5_ExerciseTask1_state1.png, M5_ExerciseTask1_state2.png) in Copilot Chat (Agent mode), then run:

```text
Build the FocusModeCard component with two states - same layout, only the button changes. I am sharing screenshots of two UI states. Build components that implement both states.

When the user is not in focus view, the button should say 'View Insights' and take them to the insights view.
When they are in focus view, it should say 'Back to Tasks' and return them to the task list.

The card has a dark purple gradient background, a bold white 'Focus Mode' title, and a subtitle that reads '{count} high-impact tasks waiting'.

Use the existing Button component - don't create a new one. One component, one return statement, no duplicated layout.

Wire this into App.tsx for full navigation behavior so I can see how it works
```

### Why each section matters

| Section | What it protects |
| --- | --- |
| Two-state instruction | Forces one component to handle both states |
| Button behavior lines | Defines both label and click action; prevents static labels |
| Subtitle format | Prevents vague text and ensures dynamic count rendering |
| Reuse existing Button | Prevents duplicate button implementations |
| One return / no duplicated layout | Enforces true variant modeling over copy-paste UI |
| Wire into App.tsx | Prevents a valid component that is never integrated |

### The constraint that matters most

The strongest guard is: "Use the existing Button component - don't create a new one. One component, one return statement, no duplicated layout."

Without this, Copilot often creates parallel markup blocks for each state, which works visually but weakens maintainability and breaks the variant-pattern objective.


### Takeaway

MCP improves value extraction from design. Variant prompting still requires explicit behavioral constraints and integration scope. One component, prop-driven state, and explicit wiring is the reliable pattern.
