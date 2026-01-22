# state-avoid-redundant

**Impact: HIGH (bug prevention)**

Don't store values in state if they can be calculated from existing props or state. Calculate during render instead.

## Why This Matters

Redundant state creates bugs because you must remember to update it whenever its source data changes. It's also slower (extra renders) and more code to maintain.

## Key Question

Can this value be calculated from existing props or state? If yes, it's not state.

## Incorrect Examples

```jsx
// ❌ Bad: fullName can be calculated
function Profile() {
  const [firstName, setFirstName] = useState('Alice');
  const [lastName, setLastName] = useState('Smith');
  const [fullName, setFullName] = useState('Alice Smith'); // Redundant!
  
  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
    setFullName(e.target.value + ' ' + lastName); // Must sync manually!
  }
}

// ❌ Bad: Item count can be calculated
function Cart() {
  const [items, setItems] = useState([]);
  const [itemCount, setItemCount] = useState(0); // Redundant!
  
  function addItem(item) {
    const newItems = [...items, item];
    setItems(newItems);
    setItemCount(newItems.length); // Must sync manually!
  }
}

// ❌ Bad: Storing prop in state (usually wrong)
function Card({ theme }) {
  const [currentTheme, setCurrentTheme] = useState(theme); // Redundant!
  // currentTheme won't update when theme prop changes!
}
```

## Correct Examples

```jsx
// ✅ Good: Calculate during render
function Profile() {
  const [firstName, setFirstName] = useState('Alice');
  const [lastName, setLastName] = useState('Smith');
  
  const fullName = firstName + ' ' + lastName; // Derived!
  
  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
    // fullName updates automatically
  }
}

// ✅ Good: Derive from existing state
function Cart() {
  const [items, setItems] = useState([]);
  
  const itemCount = items.length; // Derived!
  const total = items.reduce((sum, item) => sum + item.price, 0); // Derived!
  
  function addItem(item) {
    setItems([...items, item]);
    // Count and total update automatically
  }
}

// ✅ Good: Use prop directly
function Card({ theme }) {
  // Just use theme prop directly!
  return <div className={theme}>...</div>;
}
```

## Thinking in React: What is State?

Ask these questions:
1. Does it remain unchanged? → Not state
2. Is it passed from parent via props? → Not state  
3. Can you compute it from existing state/props? → Not state!

What's left is probably state.

## References

- [Avoid redundant state](https://react.dev/learn/choosing-the-state-structure#avoid-redundant-state)
- [Find the minimal representation of state](https://react.dev/learn/thinking-in-react#step-3-find-the-minimal-but-complete-representation-of-ui-state)
