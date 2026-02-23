---
title: Component Naming Convention
impact: HIGH
impactDescription: prevents rendering issues and confusion
tags: react, components, naming, convention
---

## Component Naming Convention

React component names must start with a capital letter. This is how React distinguishes components from HTML tags. Lowercase names are treated as built-in HTML elements.

**Incorrect (lowercase component name):**

```tsx
// This will not render correctly
function myButton() {
  return <button>Click me</button>
}

// Usage - React treats 'myButton' as an HTML tag
<MyApp />
```

**Correct (capitalized component name):**

```tsx
// Capital letter at the start
function MyButton() {
  return <button>Click me</button>
}

// Usage - React recognizes this as a component
function MyApp() {
  return (
    <div>
      <MyButton />
    </div>
  )
}
```

**Key Rules:**

1. Component names must start with a capital letter
2. File names should match component names (e.g., `MyButton.tsx`)
3. Use PascalCase for multi-word names (e.g., `UserProfile`, `NavigationBar`)

Reference: [Your First Component](https://react.dev/learn/your-first-component)
