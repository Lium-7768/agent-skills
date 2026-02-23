---
title: Never Define Components Inside Other Components
impact: HIGH
impactDescription: prevents infinite re-renders and performance issues
tags: react, components, nesting, performance
---

## Never Define Components Inside Other Components

Never define a component inside another component. This causes the inner component to be recreated on every render, leading to performance issues and broken state.

**Incorrect (component defined inside another):**

```tsx
function Parent() {
  // ❌ Child is recreated on every Parent render!
  function Child() {
    const [count, setCount] = useState(0)
    return <button onClick={() => setCount(c => c + 1)}>{count}</button>
  }

  return (
    <div>
      <Child /> {/* State resets on every Parent render */}
    </div>
  )
}
```

**Correct (components defined at top level):**

```tsx
// ✅ Define at the top level
function Child() {
  const [count, setCount] = useState(0)
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>
}

function Parent() {
  return (
    <div>
      <Child /> {/* Stable component, state preserved */}
    </div>
  )
}
```

**Why this matters:**

1. Nested components are recreated on every parent render
2. Child component state resets unexpectedly
3. React sees them as new components each time
4. Breaks memoization and optimization

**Alternative patterns when you need parent data:**

```tsx
// ✅ Pass data as props instead
function Child({ message }: { message: string }) {
  return <div>{message}</div>
}

function Parent() {
  const [data, setData] = useState('hello')
  return <Child message={data} />
}
```

Reference: [Your First Component](https://react.dev/learn/your-first-component)
