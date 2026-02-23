---
title: Create Custom Hooks for Reusable Logic
impact: MEDIUM
impactDescription: shares logic between components cleanly
tags: react, hooks, custom-hooks, patterns, reuse
---

## Create Custom Hooks for Reusable Logic

Custom hooks let you share stateful logic between components. Always start hook names with `use` so React can check for rule violations.

### Custom Hook Rules

1. Name must start with `use` (e.g., `useWindowSize`, `useFetch`)
2. Only call hooks at the top level
3. Only call hooks from React functions

**Incorrect (copying logic between components):**

```tsx
// Component A
function ComponentA() {
  const [width, setWidth] = useState(window.innerWidth)

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth)
    window.addEventListener('resize', handleResize)
    return () => window.removeEventListener('resize', handleResize)
  }, [])

  return <div>Width: {width}</div>
}

// Component B - same logic duplicated!
function ComponentB() {
  const [width, setWidth] = useState(window.innerWidth)

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth)
    window.addEventListener('resize', handleResize)
    return () => window.removeEventListener('resize', handleResize)
  }, [])

  return <div>Window is {width}px wide</div>
}
```

**Correct (custom hook):**

```tsx
// Custom hook encapsulates reusable logic
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  })

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      })
    }

    window.addEventListener('resize', handleResize)
    return () => window.removeEventListener('resize', handleResize)
  }, [])

  return size
}

// Components use the hook
function ComponentA() {
  const { width } = useWindowSize()
  return <div>Width: {width}</div>
}

function ComponentB() {
  const { width, height } = useWindowSize()
  return <div>Window is {width}x{height}</div>
}
```

### Common Custom Hook Patterns

```tsx
// Fetch data
function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null)
  const [loading, setLoading] = useState(true)
  const [error, setError] = useState<Error | null>(null)

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false))
  }, [url])

  return { data, loading, error }
}

// Local storage
function useLocalStorage<T>(key: string, initialValue: T) {
  const [value, setValue] = useState<T>(() => {
    const stored = localStorage.getItem(key)
    return stored ? JSON.parse(stored) : initialValue
  })

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value))
  }, [key, value])

  return [value, setValue] as const
}

// Previous value
function usePrevious<T>(value: T): T | undefined {
  const ref = useRef<T>()
  useEffect(() => {
    ref.current = value
  }, [value])
  return ref.current
}

// Debounced value
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value)

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay)
    return () => clearTimeout(timer)
  }, [value, delay])

  return debouncedValue
}

// Toggle
function useToggle(initial = false): [boolean, () => void] {
  const [value, setValue] = useState(initial)
  const toggle = useCallback(() => setValue(v => !v), [])
  return [value, toggle]
}
```

### Hooks Share Logic, Not State

```tsx
// Each component gets its own state
function App() {
  return (
    <>
      <Counter />  {/* Has its own count state */}
      <Counter />  {/* Has its own count state - not shared! */}
    </>
  )
}

function Counter() {
  const [count, setCount] = useCounter()  // Separate state instance
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>
}
```

Reference: [Reusing Logic with Custom Hooks](https://react.dev/learn/reusing-logic-with-custom-hooks)
