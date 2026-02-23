# hooks-never-pass-as-value

**Impact: HIGH (breaks React's optimization)**

Never pass Hooks as regular values. Hooks should only be called inside components or other Hooks.

## Why This Matters

Hooks allow React to augment components with features like state and effects. When you pass Hooks around as values:
- React can't optimize your component
- Local reasoning breaks (can't understand component by reading it alone)
- Hook rules may be violated

## Incorrect Examples

```jsx
// ❌ Bad: Passing Hook as prop
function App() {
  return <Child useTheme={useTheme} />;
}

function Child({ useTheme }) {
  const theme = useTheme(); // Hook passed as value
  return <div className={theme}>...</div>;
}

// ❌ Bad: Storing Hook in variable to use later
function Bad() {
  const hook = condition ? useThemeA : useThemeB;
  const value = hook(); // Conditionally using different Hooks
}

// ❌ Bad: Dynamically deciding which Hook to use
function Bad({ hookName }) {
  const hooks = { useTheme, useUser, useAuth };
  const value = hooks[hookName](); // Dynamic Hook usage
}
```

## Correct Examples

```jsx
// ✅ Good: Call Hook directly at top level
function App() {
  const theme = useTheme(); // Call Hook directly
  return <Child theme={theme} />;
}

function Child({ theme }) {
  return <div className={theme}>...</div>;
}

// ✅ Good: Create custom Hook for conditional logic
function useTheme(variant) {
  // Logic inside the Hook, not in selection
  if (variant === 'dark') {
    return useDarkTheme();
  }
  return useLightTheme();
}

// ✅ Good: Use single Hook with parameters
function Good({ themeType }) {
  const theme = useTheme(themeType); // Pass param, not Hook
  return <div className={theme}>...</div>;
}
```

## The Pattern

Instead of passing Hooks:
1. Call Hooks at the top level of your component
2. Pass the **results** of Hooks as props
3. Use custom Hooks to encapsulate conditional Hook logic

## References

- [Never pass around Hooks as regular values](https://react.dev/reference/rules/react-calls-components-and-hooks#never-pass-around-hooks-as-regular-values)
