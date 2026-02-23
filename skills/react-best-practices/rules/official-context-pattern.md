---
title: Use Context for Deep Prop Passing
impact: MEDIUM
impactDescription: eliminates prop drilling and simplifies component interfaces
tags: react, context, props, drilling, patterns
---

## Use Context for Deep Prop Passing

When you need to pass data through many levels of components (prop drilling), use Context instead. Context lets a parent make data available to any component in the tree below it.

**Incorrect (prop drilling):**

```tsx
function App() {
  const [user, setUser] = useState({ name: 'Alice', theme: 'dark' })
  return <Layout user={user} setUser={setUser} />
}

function Layout({ user, setUser }) {
  return (
    <div>
      <Sidebar user={user} setUser={setUser} />
      <Content />
    </div>
  )
}

function Sidebar({ user, setUser }) {
  return (
    <nav>
      <UserMenu user={user} setUser={setUser} />  {/* Still passing through */}
    </nav>
  )
}

function UserMenu({ user, setUser }) {
  return <div>{user.name}</div>  // Finally used here
}
```

**Correct (Context pattern):**

```tsx
// 1. Create context
const UserContext = createContext(null)
const UserDispatchContext = createContext(null)

// 2. Provider component
function UserProvider({ children }) {
  const [user, dispatch] = useReducer(userReducer, initialState)

  return (
    <UserContext.Provider value={user}>
      <UserDispatchContext.Provider value={dispatch}>
        {children}
      </UserDispatchContext.Provider>
    </UserContext.Provider>
  )
}

// 3. Custom hooks for consumers
function useUser() {
  return useContext(UserContext)
}

function useUserDispatch() {
  return useContext(UserDispatchContext)
}

// Usage
function App() {
  return (
    <UserProvider>
      <Layout />
    </UserProvider>
  )
}

function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  )
}

function UserMenu() {
  const user = useUser()  // ✅ Direct access, no props
  return <div>{user.name}</div>
}
```

### Context + Reducer Pattern

```tsx
// tasks-context.tsx
import { createContext, useContext, useReducer } from 'react'

const TasksContext = createContext(null)
const TasksDispatchContext = createContext(null)

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks)

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  )
}

export function useTasks() {
  return useContext(TasksContext)
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext)
}

// Component usage
function TaskList() {
  const tasks = useTasks()
  return <ul>{tasks.map(task => <li key={task.id}>{task.text}</li>)}</ul>
}

function AddTask() {
  const dispatch = useTasksDispatch()
  return (
    <button onClick={() => dispatch({ type: 'added', text: 'New task' })}>
      Add
    </button>
  )
}
```

### When to Use Context

- Theme, user preferences
- Current user/session
- Routing/location
- Any data needed by many components

### When NOT to Use Context

- Simple prop passing (just use props)
- Frequently changing data (causes many re-renders)
- Component-specific state

Reference: [Passing Data Deeply with Context](https://react.dev/learn/passing-data-deeply-with-context), [Scaling Up with Reducer and Context](https://react.dev/learn/scaling-up-with-reducer-and-context)
