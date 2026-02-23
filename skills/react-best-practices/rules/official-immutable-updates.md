---
title: Update Objects and Arrays Immutably
impact: HIGH
impactDescription: ensures correct re-renders and React DevTools tracking
tags: react, state, immutability, objects, arrays
---

## Update Objects and Arrays Immutably

In React, you should treat state as immutable. Always create new copies when updating objects or arrays in state.

### Object Updates

**Incorrect (mutating object):**

```tsx
function Form() {
  const [user, setUser] = useState({ name: 'Alice', age: 25 })

  function handleBirthday() {
    user.age = user.age + 1  // ❌ Mutation!
    setUser(user)  // React won't detect change (same reference)
  }
}
```

**Correct (create new object):**

```tsx
function Form() {
  const [user, setUser] = useState({ name: 'Alice', age: 25 })

  function handleBirthday() {
    setUser({ ...user, age: user.age + 1 })  // ✅ New object
  }
}
```

### Nested Object Updates

**Incorrect:**

```tsx
setPerson({
  ...person,
  artwork: person.artwork  // ❌ Still same reference
})
```

**Correct (copy at each level):**

```tsx
setPerson({
  ...person,
  artwork: {
    ...person.artwork,  // ✅ Copy nested object too
    city: 'New York'
  }
})
```

### Array Operations

**Incorrect (mutating methods):**

```tsx
// ❌ All of these mutate the original array
items.push(newItem)
items.pop()
items.splice(index, 1)
items[index] = newItem
items.sort()
```

**Correct (immutable patterns):**

```tsx
// Adding
setItems([...items, newItem])
setItems([newItem, ...items])  // prepend

// Removing
setItems(items.filter(item => item.id !== id))

// Replacing
setItems(items.map(item =>
  item.id === id ? { ...item, completed: true } : item
))

// Inserting at index
setItems([
  ...items.slice(0, index),
  newItem,
  ...items.slice(index)
])

// Sorting (create new array)
setItems([...items].sort((a, b) => a.name.localeCompare(b.name)))
```

### Using Immer for Complex Updates

```tsx
import { useImmer } from 'use-immer'

function Form() {
  const [person, updatePerson] = useImmer({
    name: 'Alice',
    artwork: {
      city: 'Hamburg'
    }
  })

  function handleCityChange(e) {
    updatePerson(draft => {
      draft.artwork.city = e.target.value  // ✅ Can mutate draft!
    })
  }
}
```

Reference: [Updating Objects in State](https://react.dev/learn/updating-objects-in-state), [Updating Arrays in State](https://react.dev/learn/updating-arrays-in-state)
