---
title: Use Refs for Non-Render Values
impact: MEDIUM
impactDescription: prevents unnecessary re-renders and correctly separates concerns
tags: react, hooks, useRef, useState, references
---

## Use Refs for Non-Render Values

Use `useRef` for values that don't affect rendering and don't need to trigger re-renders when they change. Use `useState` for values that are displayed in the UI.

### useRef vs useState

| useRef | useState |
|--------|----------|
| Doesn't trigger re-render | Triggers re-render |
| Mutable (change `.current`) | Immutable (use setter) |
| Persists across renders | Persists across renders |
| Not for display | For displayed values |

**Incorrect (state for non-display values):**

```tsx
function Timer() {
  const [intervalId, setIntervalId] = useState(null)  // ❌ Causes re-render

  function start() {
    const id = setInterval(() => { /* ... */ }, 1000)
    setIntervalId(id)  // Unnecessary re-render
  }

  function stop() {
    clearInterval(intervalId)
  }
}
```

**Correct (ref for non-display values):**

```tsx
function Timer() {
  const intervalRef = useRef(null)  // ✅ No re-render on change

  function start() {
    intervalRef.current = setInterval(() => { /* ... */ }, 1000)
  }

  function stop() {
    clearInterval(intervalRef.current)
  }
}
```

### Common Ref Use Cases

```tsx
// 1. DOM element references
function Input() {
  const inputRef = useRef(null)
  return <input ref={inputRef} />
}

// 2. Storing previous values
function usePrevious(value) {
  const ref = useRef()
  useEffect(() => {
    ref.current = value
  }, [value])
  return ref.current
}

// 3. Timeout/interval IDs
const timeoutRef = useRef()
timeoutRef.current = setTimeout(() => {}, 1000)

// 4. Any mutable value not used in render
const countRef = useRef(0)
countRef.current += 1  // Track renders without triggering one
```

### Never Read/Write ref.current During Render

**Incorrect:**

```tsx
function Component() {
  const ref = useRef(0)

  ref.current = ref.current + 1  // ❌ Writing during render

  return <div>{ref.current}</div>  // ❌ Reading during render (use state instead)
}
```

**Correct:**

```tsx
function Component() {
  const ref = useRef(0)
  const [count, setCount] = useState(0)

  function handleClick() {
    ref.current += 1  // ✅ Write in event handler
    console.log(ref.current)
  }

  return (
    <>
      <div>{count}</div>  {/* ✅ Display state, not ref */}
      <button onClick={handleClick}>Click</button>
    </>
  )
}
```

Reference: [Referencing Values with Refs](https://react.dev/learn/referencing-values-with-refs)
