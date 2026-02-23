# React Core Patterns

**Version 1.0.0**  
Based on React Official Documentation  
January 2026

> **Note:**  
> This document contains essential React best practices from the official React documentation at react.dev.
> These are fundamental rules for writing correct, predictable React code.

---

## Abstract

Essential React patterns and rules from official documentation. Contains 14 rules across 6 categories covering Rules of Hooks, component purity, effect best practices, state structure, key props, and accessibility. Each rule includes explanations, incorrect/correct examples, and references to official documentation.

---

## Table of Contents

1. [Rules of Hooks](#1-rules-of-hooks) — **CRITICAL**
   - 1.1 [Only Call Hooks at the Top Level](#11-only-call-hooks-at-the-top-level)
   - 1.2 [Only Call Hooks from React Functions](#12-only-call-hooks-from-react-functions)
2. [Component Purity](#2-component-purity) — **CRITICAL**
   - 2.1 [Components Must Be Idempotent](#21-components-must-be-idempotent)
   - 2.2 [Side Effects Must Run Outside of Render](#22-side-effects-must-run-outside-of-render)
   - 2.3 [Props and State Are Immutable](#23-props-and-state-are-immutable)
3. [Effect Best Practices](#3-effect-best-practices) — **HIGH**
   - 3.1 [Avoid Unnecessary Effects](#31-avoid-unnecessary-effects)
   - 3.2 [Derive State During Render](#32-derive-state-during-render)
   - 3.3 [Use Effects for External Synchronization](#33-use-effects-for-external-synchronization)
4. [State Structure](#4-state-structure) — **HIGH**
   - 4.1 [Group Related State](#41-group-related-state)
   - 4.2 [Avoid Redundant State](#42-avoid-redundant-state)
   - 4.3 [Avoid Duplication in State](#43-avoid-duplication-in-state)
   - 4.4 [Avoid Deeply Nested State](#44-avoid-deeply-nested-state)
5. [Key Props](#5-key-props) — **MEDIUM**
   - 5.1 [Use Unique Stable Keys](#51-use-unique-stable-keys)
6. [Accessibility](#6-accessibility) — **MEDIUM**
   - 6.1 [Use Semantic HTML](#61-use-semantic-html)

---

## 1. Rules of Hooks

**Impact: CRITICAL**

Hooks have fundamental rules that must be followed for React to work correctly.

### 1.1 Only Call Hooks at the Top Level

**Impact: CRITICAL (React will not work correctly if violated)**

Only call Hooks at the top level of your React function. Don't call Hooks inside loops, conditions, nested functions, or try/catch/finally blocks.

**Incorrect**

```jsx
function Bad({ cond }) {
  if (cond) {
    const [count, setCount] = useState(0); // ❌ Inside condition
  }
}

function Bad() {
  for (let i = 0; i < 10; i++) {
    const theme = useContext(ThemeContext); // ❌ Inside loop
  }
}

function Bad({ cond }) {
  if (cond) return null;
  const [count, setCount] = useState(0); // ❌ After conditional return
}
```

**Correct**

```jsx
function Good({ cond }) {
  const [count, setCount] = useState(0); // ✅ Top level
  const theme = useContext(ThemeContext);
  
  if (cond) return null; // Condition after all Hooks
  
  return <div>{count}</div>;
}
```

Reference: [Rules of Hooks](https://react.dev/reference/rules/rules-of-hooks#only-call-hooks-at-the-top-level)

### 1.2 Only Call Hooks from React Functions

**Impact: CRITICAL**

Only call Hooks from React function components or custom Hooks.

**Incorrect**

```jsx
function regularFunction() {
  const [count, setCount] = useState(0); // ❌ Not a component
}

class BadComponent extends React.Component {
  render() {
    const [count, setCount] = useState(0); // ❌ Class component
  }
}
```

**Correct**

```jsx
function Counter() { // ✅ Function component
  const [count, setCount] = useState(0);
  return <button>{count}</button>;
}

function useCounter() { // ✅ Custom Hook (starts with "use")
  const [count, setCount] = useState(0);
  return { count, increment: () => setCount(c => c + 1) };
}
```

Reference: [Rules of Hooks](https://react.dev/reference/rules/rules-of-hooks#only-call-hooks-from-react-functions)

---

## 2. Component Purity

**Impact: CRITICAL**

Components and Hooks must be pure for React's optimization and rendering model to work correctly.

### 2.1 Components Must Be Idempotent

**Impact: CRITICAL**

Components must always return the same output for the same inputs (props, state, context).

**Incorrect**

```jsx
function Clock() {
  const time = new Date(); // ❌ Different value each render
  return <span>{time.toLocaleString()}</span>;
}

function RandomColor() {
  const color = `hsl(${Math.random() * 360}, 100%, 50%)`; // ❌ Random each render
  return <div style={{ background: color }}>Hello</div>;
}
```

**Correct**

```jsx
function Clock() {
  const [time, setTime] = useState(() => new Date());
  
  useEffect(() => {
    const id = setInterval(() => setTime(new Date()), 1000);
    return () => clearInterval(id);
  }, []);
  
  return <span>{time.toLocaleString()}</span>;
}

function RandomColor() {
  const [color] = useState(
    () => `hsl(${Math.random() * 360}, 100%, 50%)` // ✅ Set once
  );
  return <div style={{ background: color }}>Hello</div>;
}
```

Reference: [Components must be idempotent](https://react.dev/reference/rules/components-and-hooks-must-be-pure#components-and-hooks-must-be-idempotent)

### 2.2 Side Effects Must Run Outside of Render

**Impact: CRITICAL**

Never perform side effects directly in the component body—use Effects or event handlers.

**Incorrect**

```jsx
function UserProfile({ userId }) {
  fetch(`/api/users/${userId}`); // ❌ Runs every render
  return <div>Loading...</div>;
}

function App() {
  document.title = 'My App'; // ❌ Side effect in render
  return <div>App</div>;
}
```

**Correct**

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => { // ✅ In Effect
    let cancelled = false;
    fetch(`/api/users/${userId}`)
      .then(res => res.json())
      .then(data => { if (!cancelled) setUser(data); });
    return () => { cancelled = true; };
  }, [userId]);
  
  return user ? <div>{user.name}</div> : <div>Loading...</div>;
}

function App() {
  useEffect(() => { // ✅ In Effect
    document.title = 'My App';
  }, []);
  
  return <div>App</div>;
}
```

Reference: [Side effects must run outside of render](https://react.dev/reference/rules/components-and-hooks-must-be-pure#side-effects-must-run-outside-of-render)

### 2.3 Props and State Are Immutable

**Impact: CRITICAL**

Never mutate props, state, or values after they've been passed to JSX.

**Incorrect**

```jsx
function Counter() {
  const [items, setItems] = useState([1, 2, 3]);
  
  function addItem() {
    items.push(4); // ❌ Mutating state
    setItems(items); // Same reference
  }
}

function Profile() {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });
  
  function birthday() {
    user.age++; // ❌ Mutating
    setUser(user);
  }
}
```

**Correct**

```jsx
function Counter() {
  const [items, setItems] = useState([1, 2, 3]);
  
  function addItem() {
    setItems([...items, 4]); // ✅ New array
  }
}

function Profile() {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });
  
  function birthday() {
    setUser({ ...user, age: user.age + 1 }); // ✅ New object
  }
}
```

Reference: [Props and state are immutable](https://react.dev/reference/rules/components-and-hooks-must-be-pure#props-and-state-are-immutable)

---

## 3. Effect Best Practices

**Impact: HIGH**

Understanding when to use (and not use) Effects is essential for React applications.

### 3.1 Avoid Unnecessary Effects

**Impact: HIGH**

Don't use Effects to transform data for rendering or handle user events.

**Incorrect**

```jsx
function TodoList({ todos, filter }) {
  const [filtered, setFiltered] = useState([]);
  
  useEffect(() => { // ❌ Unnecessary Effect
    setFiltered(todos.filter(t => t.status === filter));
  }, [todos, filter]);
}

function Form() {
  const [submitted, setSubmitted] = useState(false);
  
  useEffect(() => { // ❌ Should be in event handler
    if (submitted) {
      sendAnalytics('form_submit');
    }
  }, [submitted]);
}
```

**Correct**

```jsx
function TodoList({ todos, filter }) {
  const filtered = todos.filter(t => t.status === filter); // ✅ During render
}

function Form() {
  function handleSubmit() {
    sendAnalytics('form_submit'); // ✅ In event handler
  }
}
```

Reference: [You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect)

### 3.2 Derive State During Render

**Impact: HIGH**

Don't store derived data in state. Calculate it during render.

**Incorrect**

```jsx
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const [fullName, setFullName] = useState(''); // ❌ Redundant
  
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
}
```

**Correct**

```jsx
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  
  const fullName = firstName + ' ' + lastName; // ✅ Derived
}
```

Reference: [Updating state based on props or state](https://react.dev/learn/you-might-not-need-an-effect#updating-state-based-on-props-or-state)

### 3.3 Use Effects for External Synchronization

**Impact: MEDIUM**

Effects are for synchronizing React with external systems.

**Correct use cases:**

```jsx
// Document title
useEffect(() => {
  document.title = title;
}, [title]);

// WebSocket subscription
useEffect(() => {
  const connection = createConnection(roomId);
  connection.on('message', handleMessage);
  return () => connection.disconnect();
}, [roomId]);

// DOM measurement
useEffect(() => {
  setHeight(ref.current.getBoundingClientRect().height);
});
```

Reference: [Synchronizing with Effects](https://react.dev/learn/synchronizing-with-effects)

---

## 4. State Structure

**Impact: HIGH**

How you structure state affects code quality and bug frequency.

### 4.1 Group Related State

**Impact: MEDIUM**

If two state variables always change together, merge them.

**Incorrect**

```jsx
const [x, setX] = useState(0);
const [y, setY] = useState(0);

function handleMove(e) {
  setX(e.clientX);
  setY(e.clientY); // Easy to forget one
}
```

**Correct**

```jsx
const [position, setPosition] = useState({ x: 0, y: 0 });

function handleMove(e) {
  setPosition({ x: e.clientX, y: e.clientY });
}
```

Reference: [Group related state](https://react.dev/learn/choosing-the-state-structure#group-related-state)

### 4.2 Avoid Redundant State

**Impact: HIGH**

Don't store values that can be calculated from existing state/props.

**Incorrect**

```jsx
const [items, setItems] = useState([]);
const [itemCount, setItemCount] = useState(0); // ❌ Redundant

function addItem(item) {
  setItems([...items, item]);
  setItemCount(items.length + 1); // Must sync manually
}
```

**Correct**

```jsx
const [items, setItems] = useState([]);
const itemCount = items.length; // ✅ Derived

function addItem(item) {
  setItems([...items, item]); // Count updates automatically
}
```

Reference: [Avoid redundant state](https://react.dev/learn/choosing-the-state-structure#avoid-redundant-state)

### 4.3 Avoid Duplication in State

**Impact: MEDIUM**

Don't store the same data in multiple state variables.

**Incorrect**

```jsx
const [items, setItems] = useState([...]);
const [selectedItem, setSelectedItem] = useState(items[0]); // ❌ Duplicated
```

**Correct**

```jsx
const [items, setItems] = useState([...]);
const [selectedId, setSelectedId] = useState(items[0]?.id); // ✅ Just ID
const selectedItem = items.find(i => i.id === selectedId); // Derived
```

Reference: [Avoid duplication in state](https://react.dev/learn/choosing-the-state-structure#avoid-duplication-in-state)

### 4.4 Avoid Deeply Nested State

**Impact: MEDIUM**

Prefer flat state structures for easier updates.

**Incorrect**

```jsx
const [state, setState] = useState({
  user: { profile: { settings: { theme: 'dark' } } }
});

// Painful update
setState({ ...state, user: { ...state.user, profile: { ...state.user.profile, settings: { ...state.user.profile.settings, theme: 'light' } } } });
```

**Correct**

```jsx
const [theme, setTheme] = useState('dark');
setTheme('light'); // Simple!
```

Reference: [Avoid deeply nested state](https://react.dev/learn/choosing-the-state-structure#avoid-deeply-nested-state)

---

## 5. Key Props

**Impact: MEDIUM**

Keys help React identify which items have changed in lists.

### 5.1 Use Unique Stable Keys

**Impact: MEDIUM**

Always use unique, stable keys for list items.

**Incorrect**

```jsx
{todos.map((todo, index) => (
  <TodoItem key={index} todo={todo} /> // ❌ Index for dynamic list
))}

{items.map(item => (
  <Item key={Math.random()} item={item} /> // ❌ New key every render
))}
```

**Correct**

```jsx
{todos.map(todo => (
  <TodoItem key={todo.id} todo={todo} /> // ✅ Unique ID from data
))}
```

Reference: [Rendering Lists](https://react.dev/learn/rendering-lists#keeping-list-items-in-order-with-key)

---

## 6. Accessibility

**Impact: MEDIUM**

Accessible applications work for all users.

### 6.1 Use Semantic HTML

**Impact: MEDIUM**

Use semantic HTML elements for built-in accessibility.

**Incorrect**

```jsx
<div onClick={handleClick}>Click me</div> // ❌ div for button
<div className="nav"><div>Home</div></div> // ❌ div for nav
```

**Correct**

```jsx
<button onClick={handleClick}>Click me</button> // ✅ button
<nav><a href="/">Home</a></nav> // ✅ semantic elements
```

Also remember:
- Use `<label htmlFor="...">` for form inputs
- Use `alt` text for images
- Use `aria-*` attributes for custom components
- Manage focus for modals/dialogs

Reference: [Web Accessibility Initiative (WAI)](https://www.w3.org/WAI/)
