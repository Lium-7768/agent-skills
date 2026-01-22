# rendering-logical-and-pitfall

**Impact: MEDIUM (prevents rendering bugs with 0)**

When using logical AND (`&&`) for conditional rendering, don't put numbers on the left side. JavaScript will render `0` instead of nothing.

## Why This Matters

JavaScript's `&&` operator returns the left side if it's falsy. The number `0` is falsy but is a valid React child that renders as "0". This causes unexpected UI bugs.

## Incorrect Examples

```jsx
// ❌ Bad: Number on left side of &&
function Messages({ messageCount }) {
  return (
    <div>
      {messageCount && <p>You have new messages!</p>}
      {/* When messageCount is 0, renders "0" not nothing! */}
    </div>
  );
}

// ❌ Bad: Array length on left side
function ItemList({ items }) {
  return (
    <div>
      {items.length && <List items={items} />}
      {/* When items is empty, renders "0"! */}
    </div>
  );
}
```

## Correct Examples

```jsx
// ✅ Good: Compare to make boolean
function Messages({ messageCount }) {
  return (
    <div>
      {messageCount > 0 && <p>You have new messages!</p>}
    </div>
  );
}

// ✅ Good: Boolean conversion
function ItemList({ items }) {
  return (
    <div>
      {items.length > 0 && <List items={items} />}
    </div>
  );
}

// ✅ Good: Use ternary for clarity
function Messages({ messageCount }) {
  return (
    <div>
      {messageCount ? <p>You have new messages!</p> : null}
    </div>
  );
}

// ✅ Good: Double negation to boolean
function ItemList({ items }) {
  return (
    <div>
      {!!items.length && <List items={items} />}
    </div>
  );
}
```

## The Rule

**Left side of `&&` must be a boolean, not a number.**

| Left Side | Result when falsy |
|-----------|------------------|
| `false` | Nothing rendered ✓ |
| `null` | Nothing rendered ✓ |
| `undefined` | Nothing rendered ✓ |
| `0` | Renders "0" ✗ |
| `""` | Nothing rendered ✓ |
| `NaN` | Renders "NaN" ✗ |

## Quick Fix

```jsx
// Change this:
{count && <Component />}

// To this:
{count > 0 && <Component />}
// or
{!!count && <Component />}
// or
{count ? <Component /> : null}
```

## References

- [Conditional Rendering - Logical AND pitfall](https://react.dev/learn/conditional-rendering#logical-and-operator-)
