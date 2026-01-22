# hooks-react-functions

**Impact: CRITICAL (React will not work correctly if violated)**

Only call Hooks from React function components or custom Hooks. Don't call Hooks from regular JavaScript functions or class components.

## Why This Matters

Hooks are designed to work with React's rendering model. Calling them outside of React functions means React cannot track or manage their state properly.

## Incorrect Examples

```jsx
// ❌ Bad: Hook in regular function
function regularFunction() {
  const [count, setCount] = useState(0); // Will break!
  return count;
}

// ❌ Bad: Hook in class component
class BadComponent extends React.Component {
  render() {
    const [count, setCount] = useState(0); // Will break!
    return <div>{count}</div>;
  }
}

// ❌ Bad: Hook inside useMemo/useReducer/useEffect callback
function Bad() {
  const style = useMemo(() => {
    const theme = useContext(ThemeContext); // Will break!
    return createStyle(theme);
  }, []);
}
```

## Correct Examples

```jsx
// ✅ Good: Hook in function component
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}

// ✅ Good: Hook in custom Hook
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);
  const increment = () => setCount(c => c + 1);
  return { count, increment };
}

// ✅ Good: Move Hook outside callback
function Good() {
  const theme = useContext(ThemeContext); // Hook at top level
  const style = useMemo(() => {
    return createStyle(theme); // Use value inside
  }, [theme]);
}
```

## Naming Convention

Custom Hooks must start with `use` followed by a capital letter (e.g., `useCounter`, `useWindowWidth`). This lets React recognize them and enforce the Rules of Hooks.

## References

- [Rules of Hooks - Only call Hooks from React functions](https://react.dev/reference/rules/rules-of-hooks#only-call-hooks-from-react-functions)
