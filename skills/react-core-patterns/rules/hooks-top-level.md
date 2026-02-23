# hooks-top-level

**Impact: CRITICAL (React will not work correctly if violated)**

Only call Hooks at the top level of your React function. Don't call Hooks inside loops, conditions, nested functions, or try/catch/finally blocks.

## Why This Matters

React relies on the order in which Hooks are called to correctly preserve state between renders. If you call Hooks conditionally or in loops, the order of Hook calls may change between renders, causing React to associate the wrong state with the wrong Hook.

## Incorrect Examples

```jsx
// ❌ Bad: Hook inside condition
function Bad({ cond }) {
  if (cond) {
    const [count, setCount] = useState(0); // Will break!
  }
}

// ❌ Bad: Hook inside loop
function Bad() {
  for (let i = 0; i < 10; i++) {
    const theme = useContext(ThemeContext); // Will break!
  }
}

// ❌ Bad: Hook after conditional return
function Bad({ cond }) {
  if (cond) {
    return null;
  }
  const [count, setCount] = useState(0); // Will break!
}

// ❌ Bad: Hook inside event handler
function Bad() {
  function handleClick() {
    const theme = useContext(ThemeContext); // Will break!
  }
}

// ❌ Bad: Hook inside try/catch
function Bad() {
  try {
    const [x, setX] = useState(0); // Will break!
  } catch {
    // ...
  }
}
```

## Correct Examples

```jsx
// ✅ Good: All Hooks at top level
function Good({ cond }) {
  const [count, setCount] = useState(0);
  const theme = useContext(ThemeContext);
  
  // Conditional logic AFTER all Hooks
  if (cond) {
    return null;
  }
  
  return <div>{count}</div>;
}

// ✅ Good: Custom Hook at top level
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);
  
  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);
  
  return width;
}
```

## ESLint Plugin

Use [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks) to catch these mistakes automatically.

## References

- [Rules of Hooks - Only call Hooks at the top level](https://react.dev/reference/rules/rules-of-hooks#only-call-hooks-at-the-top-level)
