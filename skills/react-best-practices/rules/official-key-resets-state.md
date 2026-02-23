---
title: Reset State with Key Prop
impact: MEDIUM
impactDescription: provides explicit control over component state lifecycle
tags: react, state, key, reset, lifecycle
---

## Reset State with Key Prop

You can reset a component's state by passing a different `key` to it. When the key changes, React creates a new component instance, resetting all state.

**Use case: Reset form when switching items**

**Incorrect (state persists across items):**

```tsx
function ChatApp() {
  const [recipient, setRecipient] = useState(contacts[0])

  return (
    <>
      <ContactList onSelect={setRecipient} />
      <Chat recipient={recipient} />  {/* State persists when recipient changes */}
    </>
  )
}

function Chat({ recipient }) {
  const [message, setMessage] = useState('')  // Previous message stays
  // ...
}
```

**Correct (key resets state):**

```tsx
function ChatApp() {
  const [recipient, setRecipient] = useState(contacts[0])

  return (
    <>
      <ContactList onSelect={setRecipient} />
      {/* Key change creates new instance, resetting all state */}
      <Chat key={recipient.id} recipient={recipient} />
    </>
  )
}

function Chat({ recipient }) {
  const [message, setMessage] = useState('')  // ✅ Fresh state for each recipient
  // ...
}
```

### When to Use Key for Reset

| Scenario | Example |
|----------|---------|
| Switching between items | `<Chat key={userId} />` |
| Resetting form | `<Form key={formVersion} />` |
| Re-initializing component | `<Editor key={documentId} />` |
| Clearing transient state | `<SearchFilters key={category} />` |

### Key vs Conditional Rendering

```tsx
// Option 1: Key - preserves component position, resets state
{showForm && <Form key={itemId} />}

// Option 2: Conditional - removes from tree entirely
{showForm ? <Form key={itemId} /> : null}

// Both reset state when itemId changes
// Key is preferred for same component type switching
```

### Key at Same Position

```tsx
function App() {
  const [isFirst, setIsFirst] = useState(true)

  return (
    <div>
      {isFirst ? (
        <Counter key="first" />  // Same key = state preserved
      ) : (
        <Counter key="first" />  // Same key = state preserved
      )}
      <button onClick={() => setIsFirst(!isFirst)}>Toggle</button>
    </div>
  )
}

// With different keys:
{isFirst ? (
  <Counter key="first" />   // Different key = state reset
) : (
  <Counter key="second" />  // Different key = state reset
)}
```

Reference: [Preserving and Resetting State](https://react.dev/learn/preserving-and-resetting-state)
