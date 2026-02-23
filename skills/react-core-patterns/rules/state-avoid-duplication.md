# state-avoid-duplication

**Impact: MEDIUM (bug prevention)**

Don't store the same data in multiple state variables. When data is duplicated, it's easy for copies to get out of sync.

## Why This Matters

Duplicated data means you must update multiple places when data changes. Forget one, and you have a bug.

## Incorrect Examples

```jsx
// ❌ Bad: Item duplicated in items array AND selectedItem
function Menu() {
  const [items, setItems] = useState([
    { id: 1, title: 'Pizza', price: 12 },
    { id: 2, title: 'Burger', price: 8 },
  ]);
  const [selectedItem, setSelectedItem] = useState(items[0]); // Duplicated!
  
  function updatePrice(id, newPrice) {
    setItems(items.map(item =>
      item.id === id ? { ...item, price: newPrice } : item
    ));
    // Bug! selectedItem still has old price
  }
}

// ❌ Bad: User data duplicated across components
function Dashboard() {
  const [currentUser, setCurrentUser] = useState(null);
  const [profileUser, setProfileUser] = useState(null); // Same user, duplicated!
}
```

## Correct Examples

```jsx
// ✅ Good: Store only the ID, derive the full object
function Menu() {
  const [items, setItems] = useState([
    { id: 1, title: 'Pizza', price: 12 },
    { id: 2, title: 'Burger', price: 8 },
  ]);
  const [selectedId, setSelectedId] = useState(1); // Just the ID!
  
  // Derive the selected item
  const selectedItem = items.find(item => item.id === selectedId);
  
  function updatePrice(id, newPrice) {
    setItems(items.map(item =>
      item.id === id ? { ...item, price: newPrice } : item
    ));
    // selectedItem automatically reflects the change!
  }
}

// ✅ Good: Single source of truth
function Dashboard() {
  const [currentUser, setCurrentUser] = useState(null);
  // Use currentUser everywhere, no duplication
}
```

## Pattern: Store IDs, Not Objects

When you need to track a selection from a list:
- ❌ Don't store the full object
- ✅ Store just the ID
- ✅ Find/derive the object when needed

```jsx
// ❌ Bad
const [selectedTodo, setSelectedTodo] = useState(todos[0]);

// ✅ Good
const [selectedTodoId, setSelectedTodoId] = useState(todos[0]?.id);
const selectedTodo = todos.find(t => t.id === selectedTodoId);
```

## References

- [Avoid duplication in state](https://react.dev/learn/choosing-the-state-structure#avoid-duplication-in-state)
