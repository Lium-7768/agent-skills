# component-never-call-directly

**Impact: HIGH (breaks React's optimization and Hook rules)**

Never call component functions directly. Components should only be used in JSX, not called as regular functions.

## Why This Matters

React must decide when to call your component function during rendering. When you call components directly:
- Hooks inside may violate Rules of Hooks (called in loops/conditions)
- React can't optimize re-renders
- React can't use features like Suspense, transitions, or batching
- Components lose their identity in the React tree

## Incorrect Examples

```jsx
// ❌ Bad: Calling component as function
function BlogPost() {
  return (
    <Layout>
      {Article()} {/* Direct call - BAD */}
    </Layout>
  );
}

// ❌ Bad: Calling component in variable
function App() {
  const header = Header(); // Direct call - BAD
  return <div>{header}</div>;
}

// ❌ Bad: Calling component conditionally
function Page({ showSidebar }) {
  return (
    <div>
      {showSidebar && Sidebar()} {/* Direct call in condition - BAD */}
      <Main />
    </div>
  );
}
```

## Correct Examples

```jsx
// ✅ Good: Using component in JSX
function BlogPost() {
  return (
    <Layout>
      <Article /> {/* JSX - React calls it */}
    </Layout>
  );
}

// ✅ Good: Component in JSX
function App() {
  return (
    <div>
      <Header /> {/* JSX - React calls it */}
    </div>
  );
}

// ✅ Good: Conditional rendering with JSX
function Page({ showSidebar }) {
  return (
    <div>
      {showSidebar && <Sidebar />} {/* JSX */}
      <Main />
    </div>
  );
}
```

## Benefits of Letting React Call Components

1. **Hooks work correctly** - React tracks Hook calls per component instance
2. **Efficient reconciliation** - React knows which components need re-rendering
3. **Better debugging** - React DevTools can show component hierarchy
4. **Future features** - Concurrent features, Suspense, transitions work correctly

## References

- [Never call component functions directly](https://react.dev/reference/rules/react-calls-components-and-hooks#never-call-component-functions-directly)
