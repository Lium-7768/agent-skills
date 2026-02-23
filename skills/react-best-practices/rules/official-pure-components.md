---
title: Keep Components Pure
impact: HIGH
impactDescription: ensures predictable behavior and enables React optimizations
tags: react, components, pure-functions, side-effects, strict-mode
---

## Keep Components Pure

React components should be pure functions: given the same props, they should always return the same JSX. Side effects should be handled in event handlers or effects, not during rendering.

### Pure Component Rules

1. Never modify external variables during render
2. Same inputs = same output
3. Side effects belong in event handlers or effects

**Incorrect (impure component):**

```tsx
// Global variable modified during render
let guestCount = 0

function Cup() {
  guestCount++  // ❌ Side effect during render!
  return <h2>Tea cup for guest #{guestCount}</h2>
}

function TeaSet() {
  return (
    <>
      <Cup />  {/* guest 1 */}
      <Cup />  {/* guest 2 */}
      <Cup />  {/* guest 3 - but results unpredictable! */}
    </>
  )
}
```

**Correct (pure component):**

```tsx
function Cup({ guest }: { guest: number }) {
  return <h2>Tea cup for guest #{guest}</h2>  // ✅ Pure, no side effects
}

function TeaSet() {
  return (
    <>
      <Cup guest={1} />
      <Cup guest={2} />
      <Cup guest={3} />
    </>
  )
}
```

### Common Impurity Patterns to Avoid

```tsx
// ❌ Modifying props
function Profile({ user }) {
  user.name = user.name.toUpperCase()  // Mutation!
  return <div>{user.name}</div>
}

// ❌ Modifying external state
let total = 0
function Item({ price }) {
  total += price  // Side effect!
  return <div>${price}</div>
}

// ❌ Random values in render
function RandomGreeting() {
  return <div>{Math.random() > 0.5 ? 'Hello' : 'Hi'}</div>  // Unpredictable!
}

// ❌ Current time in render
function Clock() {
  return <div>{new Date().toLocaleTimeString()}</div>  // Changes without update!
}
```

### Where Side Effects Belong

```tsx
function Form() {
  const [submitted, setSubmitted] = useState(false)

  // ❌ Not in render
  // if (submitted) sendAnalytics()

  // ✅ In event handler
  function handleSubmit(e) {
    e.preventDefault()
    sendAnalytics()  // Side effect in handler
    setSubmitted(true)
  }

  // ✅ Or in effect
  useEffect(() => {
    if (submitted) {
      sendAnalytics()
    }
  }, [submitted])

  return <form onSubmit={handleSubmit}>...</form>
}
```

### Strict Mode Helps Catch Impurities

```tsx
// In development, Strict Mode renders components twice
// This helps detect side effects during render

import { StrictMode } from 'react'

function App() {
  return (
    <StrictMode>
      <MyComponent />
    </StrictMode>
  )
}

// If your component modifies external state during render,
// Strict Mode will make this bug obvious
```

### Local Mutation is Fine

```tsx
function Cup({ tea }) {
  // ✅ Local mutation is fine - doesn't affect anything outside
  let cups = []
  for (let i = 0; i < tea; i++) {
    cups.push(<CupGraphic key={i} />)
  }
  return <div>{cups}</div>
}
```

Reference: [Keeping Components Pure](https://react.dev/learn/keeping-components-pure)
