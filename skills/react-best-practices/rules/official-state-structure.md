---
title: Design State Structure Properly
impact: HIGH
impactDescription: prevents bugs, reduces complexity, improves maintainability
tags: react, state, useState, design, structure
---

## Design State Structure Properly

Well-structured state makes components easier to debug and maintain. Follow these principles: group related state, avoid contradictions, avoid redundancy, avoid duplication, and avoid deeply nested state.

### 1. Group Related State

**Incorrect (separate related values):**

```tsx
const [x, setX] = useState(0)
const [y, setY] = useState(0)
```

**Correct (group related values):**

```tsx
const [position, setPosition] = useState({ x: 0, y: 0 })
```

### 2. Avoid Contradictions

**Incorrect (state can be contradictory):**

```tsx
const [isSending, setIsSending] = useState(false)
const [isSent, setIsSent] = useState(false)
// Both can be true at same time - contradictory!
```

**Correct (use single state for mutually exclusive conditions):**

```tsx
type Status = 'typing' | 'sending' | 'sent'
const [status, setStatus] = useState<Status>('typing')
// Can only be one value - no contradictions
```

### 3. Avoid Redundant State

**Incorrect (storing computable values):**

```tsx
const [firstName, setFirstName] = useState('')
const [lastName, setLastName] = useState('')
const [fullName, setFullName] = useState('')  // ❌ Redundant!

useEffect(() => {
  setFullName(firstName + ' ' + lastName)
}, [firstName, lastName])
```

**Correct (compute during render):**

```tsx
const [firstName, setFirstName] = useState('')
const [lastName, setLastName] = useState('')
const fullName = firstName + ' ' + lastName  // ✅ Computed
```

### 4. Avoid Duplication

**Incorrect (duplicated data):**

```tsx
const [items, setItems] = useState([
  { id: 1, name: 'Apple' },
  { id: 2, name: 'Banana' }
])
const [selectedId, setSelectedId] = useState(1)
const [selectedItem, setSelectedItem] = useState(items[0])  // ❌ Duplicate!
```

**Correct (single source of truth):**

```tsx
const [items, setItems] = useState([...])
const [selectedId, setSelectedId] = useState(1)
const selectedItem = items.find(item => item.id === selectedId)  // ✅ Derived
```

### 5. Avoid Deep Nesting

**Incorrect (deeply nested state):**

```tsx
const [state, setState] = useState({
  user: {
    profile: {
      settings: {
        theme: 'dark',
        notifications: {
          email: true
        }
      }
    }
  }
})
// Updating nested value is complex and error-prone
```

**Correct (flatten state):**

```tsx
const [theme, setTheme] = useState('dark')
const [emailNotifications, setEmailNotifications] = useState(true)
// Or use reducer for complex state
```

Reference: [Choosing the State Structure](https://react.dev/learn/choosing-the-state-structure)
