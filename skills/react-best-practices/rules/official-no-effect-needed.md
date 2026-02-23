---
title: You Might Not Need an Effect
impact: HIGH
impactDescription: eliminates unnecessary effects and improves performance
tags: react, hooks, useEffect, effects, anti-patterns
---

## You Might Not Need an Effect

Effects are for synchronizing with external systems. Many common patterns don't require effects. Using effects incorrectly leads to performance issues and bugs.

### 1. Don't Use Effects to Transform Data

**Incorrect:**

```tsx
function Form({ firstName, lastName }) {
  const [fullName, setFullName] = useState('')

  useEffect(() => {
    setFullName(firstName + ' ' + lastName)  // ❌ Unnecessary effect
  }, [firstName, lastName])

  return <div>{fullName}</div>
}
```

**Correct:**

```tsx
function Form({ firstName, lastName }) {
  const fullName = firstName + ' ' + lastName  // ✅ Calculate during render
  return <div>{fullName}</div>
}
```

### 2. Don't Use Effects to Handle User Events

**Incorrect:**

```tsx
function Form() {
  const [submitted, setSubmitted] = useState(false)

  useEffect(() => {
    if (submitted) {
      submitToServer(formData)  // ❌ Logic in effect
    }
  }, [submitted])

  function handleSubmit() {
    setSubmitted(true)  // Just sets flag
  }
}
```

**Correct:**

```tsx
function Form() {
  function handleSubmit() {
    submitToServer(formData)  // ✅ Logic in event handler
  }

  return <form onSubmit={handleSubmit}>...</form>
}
```

### 3. Don't Use Effects to Reset State Based on Props

**Incorrect:**

```tsx
function Profile({ userId }) {
  const [comment, setComment] = useState('')

  useEffect(() => {
    setComment('')  // ❌ Reset in effect
  }, [userId])
}
```

**Correct:**

```tsx
function Profile({ userId }) {
  return <Comment key={userId} />  // ✅ Key resets state automatically
}

function Comment() {
  const [comment, setComment] = useState('')  // Fresh state per key
}
```

### 4. Don't Use Effects for Expensive Calculations

**Incorrect:**

```tsx
function TodoList({ todos, filter }) {
  const [filtered, setFiltered] = useState([])

  useEffect(() => {
    setFiltered(todos.filter(todo => todo.includes(filter)))  // ❌
  }, [todos, filter])
}
```

**Correct:**

```tsx
function TodoList({ todos, filter }) {
  const filtered = useMemo(() => {
    return todos.filter(todo => todo.includes(filter))  // ✅ useMemo
  }, [todos, filter])
}
```

### When You DO Need Effects

- Subscribing to external stores
- Fetching data on mount
- Setting up/tearing down connections
- Timers and intervals
- Working with non-React APIs

```tsx
// ✅ Correct effect usage
useEffect(() => {
  const connection = createConnection(serverUrl)
  connection.connect()

  return () => connection.disconnect()  // Cleanup
}, [serverUrl])
```

Reference: [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
