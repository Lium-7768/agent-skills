# effect-avoid-unnecessary

**Impact: HIGH (performance and complexity if violated)**

Don't use Effects to transform data for rendering or handle user events. Many Effects are unnecessary and can be replaced with simpler code.

## Why This Matters

Unnecessary Effects cause extra render cycles, add complexity, and make code harder to understand. React re-renders components efficiently—use that instead of syncing state with Effects.

## When NOT to Use Effects

### 1. Transforming Data for Rendering

```jsx
// ❌ Bad: Effect to calculate derived data
function TodoList({ todos, filter }) {
  const [filteredTodos, setFilteredTodos] = useState([]);
  
  useEffect(() => {
    setFilteredTodos(todos.filter(t => t.status === filter));
  }, [todos, filter]);
}

// ✅ Good: Calculate during render
function TodoList({ todos, filter }) {
  const filteredTodos = todos.filter(t => t.status === filter);
  // ...
}
```

### 2. Handling User Events

```jsx
// ❌ Bad: Effect to respond to form submission
function Form() {
  const [submitted, setSubmitted] = useState(false);
  
  useEffect(() => {
    if (submitted) {
      sendAnalytics('form_submit');
      setSubmitted(false);
    }
  }, [submitted]);
  
  return <button onClick={() => setSubmitted(true)}>Submit</button>;
}

// ✅ Good: Handle in event handler
function Form() {
  function handleSubmit() {
    sendAnalytics('form_submit');
    // ... rest of submission logic
  }
  
  return <button onClick={handleSubmit}>Submit</button>;
}
```

### 3. Initializing Application

```jsx
// ❌ Bad: Effect that runs once on mount
function App() {
  useEffect(() => {
    initializeSDK();
  }, []);
}

// ✅ Good: Initialize outside component or use lazy initialization
initializeSDK(); // At module level

function App() {
  // Component code
}
```

## When TO Use Effects

Effects are for **synchronizing with external systems**:
- Fetching data (though libraries like SWR/React Query are better)
- Setting up subscriptions (WebSocket, DOM events)
- Syncing with browser APIs (title, localStorage)
- Controlling non-React widgets (maps, video players)

## References

- [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)
