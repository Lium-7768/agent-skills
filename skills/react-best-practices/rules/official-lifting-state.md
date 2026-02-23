---
title: Lift State Up to Closest Common Parent
impact: MEDIUM
impactDescription: enables component coordination and single source of truth
tags: react, state, lifting, sharing, components
---

## Lift State Up to Closest Common Parent

When two or more components need to share the same state, lift that state up to their closest common parent component. This creates a single source of truth.

**Incorrect (duplicate state in siblings):**

```tsx
function ChildA() {
  const [value, setValue] = useState('')  // ❌ Separate state
  return <input value={value} onChange={e => setValue(e.target.value)} />
}

function ChildB() {
  const [value, setValue] = useState('')  // ❌ Separate state (not synced!)
  return <p>Value: {value}</p>
}

function Parent() {
  return (
    <>
      <ChildA />
      <ChildB />  {/* Won't show ChildA's value */}
    </>
  )
}
```

**Correct (state in parent, passed down):**

```tsx
function ChildA({ value, onChange }) {
  return <input value={value} onChange={e => onChange(e.target.value)} />
}

function ChildB({ value }) {
  return <p>Value: {value}</p>
}

function Parent() {
  const [value, setValue] = useState('')  // ✅ Single source of truth

  return (
    <>
      <ChildA value={value} onChange={setValue} />
      <ChildB value={value} />  {/* Shows synced value */}
    </>
  )
}
```

### Steps to Lift State Up

1. Remove state from child components
2. Pass the state value down as props
3. Pass an update function down as a prop
4. Child calls the update function instead of setting state directly

### Example: Accordion Pattern

```tsx
function Accordion() {
  const [activeIndex, setActiveIndex] = useState(0)

  return (
    <>
      <Panel
        title="About"
        isActive={activeIndex === 0}
        onShow={() => setActiveIndex(0)}
      >
        Content about section
      </Panel>
      <Panel
        title="Etymology"
        isActive={activeIndex === 1}
        onShow={() => setActiveIndex(1)}
      >
        Content etymology section
      </Panel>
    </>
  )
}

function Panel({ title, children, isActive, onShow }) {
  return (
    <section>
      <h3>{title}</h3>
      {isActive ? <p>{children}</p> : <button onClick={onShow}>Show</button>}
    </section>
  )
}
```

Reference: [Sharing State Between Components](https://react.dev/learn/sharing-state-between-components)
