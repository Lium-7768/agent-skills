# purity-side-effects

**Impact: CRITICAL (bugs and unexpected behavior if violated)**

Side effects must run outside of render. Never perform side effects directly in the component body—use Effects, event handlers, or other appropriate mechanisms.

## Why This Matters

React may render components multiple times (Strict Mode, Suspense, concurrent rendering). If side effects run during render, they may execute multiple times unexpectedly, causing bugs, duplicated API calls, or corrupted state.

## Incorrect Examples

```jsx
// ❌ Bad: API call during render
function UserProfile({ userId }) {
  fetch(`/api/users/${userId}`); // Runs on every render!
  return <div>Loading...</div>;
}

// ❌ Bad: DOM manipulation during render
function App() {
  document.title = 'My App'; // Runs on every render!
  return <div>App</div>;
}

// ❌ Bad: Analytics during render
function ProductPage({ product }) {
  analytics.track('product_view', product); // Runs on every render!
  return <div>{product.name}</div>;
}

// ❌ Bad: Subscribing during render
function Chat() {
  socket.on('message', handleMessage); // Adds listener on every render!
  return <div>Chat</div>;
}
```

## Correct Examples

```jsx
// ✅ Good: Use Effect for data fetching (or better: a data fetching library)
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    let cancelled = false;
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => {
        if (!cancelled) setUser(data);
      });
    return () => { cancelled = true; };
  }, [userId]);
  
  return user ? <div>{user.name}</div> : <div>Loading...</div>;
}

// ✅ Good: Use Effect for document title
function App() {
  useEffect(() => {
    document.title = 'My App';
  }, []);
  
  return <div>App</div>;
}

// ✅ Good: Use Effect for analytics with cleanup
function ProductPage({ product }) {
  useEffect(() => {
    analytics.track('product_view', product);
  }, [product.id]);
  
  return <div>{product.name}</div>;
}

// ✅ Good: Use Effect with cleanup for subscriptions
function Chat() {
  useEffect(() => {
    socket.on('message', handleMessage);
    return () => socket.off('message', handleMessage);
  }, []);
  
  return <div>Chat</div>;
}
```

## When to Use What

| Side Effect Type | Use |
|-----------------|-----|
| Data fetching | `useEffect` (or data fetching library) |
| Subscriptions | `useEffect` with cleanup |
| DOM manipulation | `useEffect` or `useLayoutEffect` |
| In response to user action | Event handler |

## References

- [Side effects must run outside of render](https://react.dev/reference/rules/components-and-hooks-must-be-pure#side-effects-must-run-outside-of-render)
