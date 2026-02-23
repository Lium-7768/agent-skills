---
title: Use Correct Keys for List Rendering
impact: HIGH
impactDescription: prevents rendering bugs and state issues
tags: react, lists, keys, rendering
---

## Use Correct Keys for List Rendering

Keys tell React which array item each component corresponds to. Use stable, unique IDs from your data as keys. Never use array index as key unless the list is static and never reordered.

**Incorrect (using index as key):**

```tsx
function TodoList({ todos }) {
  return (
    <ul>
      {/* ❌ Index as key causes bugs when list changes */}
      {todos.map((todo, index) => (
        <li key={index}>
          <input type="checkbox" checked={todo.completed} />
          {todo.text}
        </li>
      ))}
    </ul>
  )
}
```

**Correct (use stable unique IDs):**

```tsx
function TodoList({ todos }) {
  return (
    <ul>
      {/* ✅ Use unique ID from data */}
      {todos.map((todo) => (
        <li key={todo.id}>
          <input type="checkbox" checked={todo.completed} />
          {todo.text}
        </li>
      ))}
    </ul>
  )
}
```

**Why index as key is problematic:**

```tsx
// Initial state: [A, B, C] with indices [0, 1, 2]
// After prepending D: [D, A, B, C] with indices [0, 1, 2, 3]

// With index keys:
// - React thinks index 0 still refers to A (now it's D)
// - Component state gets mixed up
// - Animations may break
// - Focus state incorrect
```

**When index is acceptable:**

```tsx
// ✅ Static list that never changes
const STATIC_ITEMS = ['Apple', 'Banana', 'Orange']

function FruitList() {
  return (
    <ul>
      {STATIC_ITEMS.map((fruit, index) => (
        <li key={index}>{fruit}</li>  // OK for truly static lists
      ))}
    </ul>
  )
}
```

**Key rules:**

1. Keys must be unique among siblings (not globally)
2. Use data's unique ID (like `id` from database)
3. Don't use index for dynamic lists
4. Don't generate keys randomly (e.g., `Math.random()`)

Reference: [Rendering Lists](https://react.dev/learn/rendering-lists)
