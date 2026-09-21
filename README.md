This module is built around a single app called **Methodical Tasks** — a task manager with a React frontend and an Express backend. The app has three views: **Tasks** (create and manage tasks), **Focus** (high-impact tasks only), and **Archive** (completed tasks).

The backend uses TypeScript, Express, and Zod for validation. The frontend uses React, TypeScript, and Tailwind CSS.

---

## Your Working Environment

You have two folders:

- **`temp-task-manager-app-frontend`** — your working directory. Everything you build through prompting goes here.
- **`task-manager-app`** — the complete reference implementation. If you get stuck, copy what you need from here and continue.

---

## Getting Started

Open two terminal tabs from the project root.

**Start the backend:**
```bash
pnpm dev:backend
```

**Start the frontend:**
```bash
pnpm dev:frontend
```

- Backend: http://localhost:3001
- Frontend: http://localhost:5174

---

## Repository Structure

```
temp-task-manager-app-frontend/
├── packages/
│   ├── backend/
│   │   └── src/
│   │       ├── app.ts           ← Express app setup, CORS, routes, error handler
│   │       ├── index.ts         ← Server entry point (port 3001)
│   │       ├── config/
│   │       │   └── index.ts     ← Port and CORS origin config
│   │       ├── controllers/
│   │       │   └── tasksController.ts  ← listTasks, createTask, patchTask, removeTask
│   │       ├── middleware/
│   │       │   └── errorHandler.ts     ← Global error handler
│   │       ├── routes/
│   │       │   └── tasksRoutes.ts      ← GET /api/tasks, POST, PATCH & DELETE /api/tasks/:id
│   │       ├── schemas/
│   │       │   └── task.ts      ← Zod schemas: createTaskBodySchema, patchTaskBodySchema, taskIdParamSchema
│   │       ├── store/
│   │       │   └── taskStore.ts ← In-memory task store with resetTaskStore()
│   │       └── types/
│   │           └── task.ts      ← Task interface (id, text, description, completed, isHighImpact)
│   └── frontend/
│   │   └── src/
│   │       ├── App.tsx          ← Root component, state management, tab routing
│   │       ├── api/
│   │       │   └── tasksApi.ts  ← fetchTasks, createTask, patchTask, deleteTask
│   │       ├── components/      ← Header, TabNavigation, HeroSection, TaskInput, TaskItem,
│   │       │                         TaskList, FocusModeCard, ArchiveActions, EmptyState, Button
│   │       └── types/
│   │           └── task.ts      ← Task interface (mirrors backend)
└── package.json                 ← pnpm workspace root, dev/build/test scripts

```


