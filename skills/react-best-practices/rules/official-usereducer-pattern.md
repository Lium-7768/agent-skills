---
title: Use useReducer for Complex State Logic
impact: MEDIUM
impactDescription: centralizes state logic and makes it testable
tags: react, hooks, useReducer, state, patterns
---

## Use useReducer for Complex State Logic

When state logic becomes complex (multiple sub-values, next state depends on previous, etc.), use `useReducer` instead of `useState`. It centralizes state update logic and makes it easier to debug and test.

**When to use useReducer:**

- Multiple related state values
- Next state depends on previous state
- Complex update logic
- Want to test state logic in isolation

**Incorrect (complex useState logic):**

```tsx
function TaskApp() {
  const [tasks, setTasks] = useState([])
  const [isLoading, setIsLoading] = useState(false)
  const [error, setError] = useState(null)

  function handleAdd(text) {
    setTasks([...tasks, { id: nextId++, text, done: false }])
  }

  function handleChange(task) {
    setTasks(tasks.map(t => t.id === task.id ? task : t))
  }

  function handleDelete(id) {
    setTasks(tasks.filter(t => t.id !== id))
  }

  // Logic scattered across handlers...
}
```

**Correct (useReducer pattern):**

```tsx
// Define reducer function (can be tested independently)
function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }]
    }
    case 'changed': {
      return tasks.map(t => {
        if (t.id === action.task.id) {
          return action.task
        } else {
          return t
        }
      })
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id)
    }
    default: {
      throw Error('Unknown action: ' + action.type)
    }
  }
}

function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks)

  function handleAdd(text) {
    dispatch({ type: 'added', id: nextId++, text })
  }

  function handleChange(task) {
    dispatch({ type: 'changed', task })
  }

  function handleDelete(id) {
    dispatch({ type: 'deleted', id })
  }

  // All update logic is in the reducer
}
```

### Reducer Best Practices

```tsx
// 1. Use descriptive action types
dispatch({ type: 'user/loggedIn', user })
dispatch({ type: 'cart/itemAdded', productId })

// 2. Keep reducers pure (no side effects)
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { ...state, count: state.count + 1 }  // ✅ Pure
    // case 'fetch':
    //   fetchData()  // ❌ No side effects in reducer!
    //   return state
  }
}

// 3. Handle all action types
function reducer(state, action) {
  switch (action.type) {
    // ... cases
    default:
      throw new Error(`Unhandled action type: ${action.type}`)
  }
}
```

Reference: [Extracting State Logic into a Reducer](https://react.dev/learn/extracting-state-logic-into-a-reducer)
