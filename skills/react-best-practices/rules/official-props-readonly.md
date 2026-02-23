---
title: Never Mutate Props Directly
impact: HIGH
impactDescription: prevents bugs and respects React's one-way data flow
tags: react, props, immutability, mutation
---

## Never Mutate Props Directly

Props are read-only. Never modify props directly - this breaks React's one-way data flow and can cause unexpected bugs.

**Incorrect (mutating props):**

```tsx
function Profile({ user }) {
  user.name = 'New Name'  // ❌ Direct mutation!

  user.preferences.theme = 'dark'  // ❌ Nested mutation!

  return <div>{user.name}</div>
}

function List({ items }) {
  items.push({ id: 4, name: 'New' })  // ❌ Array mutation!
  return items.map(item => <div key={item.id}>{item.name}</div>)
}
```

**Correct (copy and modify):**

```tsx
function Profile({ user }) {
  // ✅ Create local copy if modification needed
  const displayName = user.name.toUpperCase()

  // ✅ For objects, create new reference
  const updatedUser = { ...user, name: 'New Name' }

  return <div>{displayName}</div>
}

// If parent needs to know about changes, use callback
function Profile({ user, onUserChange }) {
  const handleChange = () => {
    // ✅ Let parent handle the update
    onUserChange({ ...user, name: 'New Name' })
  }

  return <button onClick={handleChange}>Update</button>
}
```

**Why props must be read-only:**

1. Props flow down from parent to child
2. Parent owns the data and controls updates
3. Mutation breaks React's change detection
4. Makes bugs hard to track down

**Common mutation patterns to avoid:**

```tsx
// ❌ All of these mutate props
props.array.push(item)
props.array[i] = newItem
props.object.key = value
delete props.object.key
```

Reference: [Passing Props to a Component](https://react.dev/learn/passing-props-to-a-component)
