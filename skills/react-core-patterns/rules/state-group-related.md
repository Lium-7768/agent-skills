# state-group-related

**Impact: MEDIUM (cleaner code and fewer bugs)**

If two or more state variables always change together, merge them into a single state variable.

## Why This Matters

Separate state variables that must stay in sync are a common source of bugs—it's easy to update one and forget the other.

## Incorrect Examples

```jsx
// ❌ Bad: Separate states that always change together
function MouseTracker() {
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  
  function handleMove(e) {
    setX(e.clientX);
    setY(e.clientY);
    // Easy to forget one!
  }
}

// ❌ Bad: Multiple states for form that submit together
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [error, setError] = useState(null);
  const [isSuccess, setIsSuccess] = useState(false);
  // Too many related states!
}
```

## Correct Examples

```jsx
// ✅ Good: Related values in one object
function MouseTracker() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  function handleMove(e) {
    setPosition({ x: e.clientX, y: e.clientY });
  }
}

// ✅ Good: Form state in single object or use useReducer
function LoginForm() {
  const [formState, setFormState] = useState({
    email: '',
    password: '',
    status: 'idle', // 'idle' | 'submitting' | 'success' | 'error'
    error: null
  });
  
  // Or even better, use useReducer for complex state:
}

// ✅ Good: useReducer for complex related state
function LoginForm() {
  const [state, dispatch] = useReducer(formReducer, {
    email: '',
    password: '',
    status: 'idle',
    error: null
  });
}
```

## When to Merge State

Merge when:
- Two state variables always change at the same time
- State represents a single logical concept (position, form data)
- You're unsure how many values you'll need (dynamic forms)

Keep separate when:
- State variables change independently
- States are conceptually unrelated

## References

- [Group related state](https://react.dev/learn/choosing-the-state-structure#group-related-state)
