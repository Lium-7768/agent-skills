# purity-immutable

**Impact: CRITICAL (state corruption and unexpected behavior if violated)**

Never mutate props, state, or values after they've been passed to JSX. Always create new objects/arrays when updating.

## Why This Matters

React uses reference equality to detect changes. Mutating existing objects/arrays means React cannot detect the change and won't re-render. It also breaks features like time-travel debugging and can cause subtle bugs.

## Incorrect Examples

```jsx
// ❌ Bad: Mutating state directly
function Counter() {
  const [items, setItems] = useState([1, 2, 3]);
  
  function addItem() {
    items.push(4); // Mutating state!
    setItems(items); // Same reference - React won't re-render!
  }
}

// ❌ Bad: Mutating props
function TodoItem({ todo, onUpdate }) {
  function markDone() {
    todo.done = true; // Mutating props!
    onUpdate(todo);
  }
}

// ❌ Bad: Mutating object in state
function Profile() {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });
  
  function birthday() {
    user.age++; // Mutating!
    setUser(user);
  }
}

// ❌ Bad: Mutating value after passing to JSX
function App() {
  const style = { color: 'red' };
  const element = <div style={style}>Hello</div>;
  style.color = 'blue'; // Mutating after JSX!
  return element;
}
```

## Correct Examples

```jsx
// ✅ Good: Create new array
function Counter() {
  const [items, setItems] = useState([1, 2, 3]);
  
  function addItem() {
    setItems([...items, 4]); // New array
  }
  
  function removeItem(index) {
    setItems(items.filter((_, i) => i !== index)); // New array
  }
}

// ✅ Good: Create new object for props
function TodoItem({ todo, onUpdate }) {
  function markDone() {
    onUpdate({ ...todo, done: true }); // New object
  }
}

// ✅ Good: Create new object in state
function Profile() {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });
  
  function birthday() {
    setUser({ ...user, age: user.age + 1 }); // New object
  }
}

// ✅ Good: Prepare values before JSX
function App() {
  const style = { color: 'red' };
  // Style is immutable after this point
  return <div style={style}>Hello</div>;
}
```

## Updating Patterns

| Data Type | Add | Remove | Update |
|-----------|-----|--------|--------|
| Array | `[...arr, item]` | `arr.filter()` | `arr.map(x => x.id === id ? {...x, ...update} : x)` |
| Object | `{...obj, newKey: value}` | `const {removed, ...rest} = obj` | `{...obj, key: newValue}` |

## References

- [Props and state are immutable](https://react.dev/reference/rules/components-and-hooks-must-be-pure#props-and-state-are-immutable)
- [Values are immutable after being passed to JSX](https://react.dev/reference/rules/components-and-hooks-must-be-pure#values-are-immutable-after-being-passed-to-jsx)
