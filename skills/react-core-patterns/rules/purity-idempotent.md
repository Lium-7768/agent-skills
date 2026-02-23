# purity-idempotent

**Impact: CRITICAL (unpredictable behavior if violated)**

Components must be idempotent—they must always return the same output for the same inputs (props, state, context). This is a fundamental rule that allows React to optimize rendering.

## Why This Matters

React may render components multiple times (for Strict Mode, Suspense, concurrent features). If components aren't idempotent, each render may produce different results, leading to unpredictable UI.

## Incorrect Examples

```jsx
// ❌ Bad: new Date() returns different value each time
function Clock() {
  const time = new Date(); // Non-idempotent!
  return <span>{time.toLocaleString()}</span>;
}

// ❌ Bad: Math.random() returns different value each time
function RandomColor() {
  const color = `hsl(${Math.random() * 360}, 100%, 50%)`; // Non-idempotent!
  return <div style={{ background: color }}>Hello</div>;
}

// ❌ Bad: Reading/mutating external variable during render
let renderCount = 0;
function Counter() {
  renderCount++; // Mutation during render!
  return <div>Rendered {renderCount} times</div>;
}
```

## Correct Examples

```jsx
// ✅ Good: Use Effect for time updates
function Clock() {
  const [time, setTime] = useState(() => new Date());
  
  useEffect(() => {
    const id = setInterval(() => setTime(new Date()), 1000);
    return () => clearInterval(id);
  }, []);
  
  return <span>{time.toLocaleString()}</span>;
}

// ✅ Good: Use state initialized once with random value
function RandomColor() {
  const [color] = useState(
    () => `hsl(${Math.random() * 360}, 100%, 50%)`
  );
  return <div style={{ background: color }}>Hello</div>;
}

// ✅ Good: Use ref for values that shouldn't trigger re-render
function Counter() {
  const renderCount = useRef(0);
  
  useEffect(() => {
    renderCount.current++;
  });
  
  return <div>Component mounted</div>;
}
```

## Key Principle

Move non-idempotent operations:
- Into `useState` initializer (runs once)
- Into `useEffect` (runs outside render)
- Into event handlers (runs on user action)

## References

- [Components and Hooks must be idempotent](https://react.dev/reference/rules/components-and-hooks-must-be-pure#components-and-hooks-must-be-idempotent)
- [Keeping Components Pure](https://react.dev/learn/keeping-components-pure)
