---
title: Use Props Destructuring with Default Values
impact: MEDIUM
impactDescription: improves readability and handles missing props gracefully
tags: react, props, destructuring, default-values
---

## Use Props Destructuring with Default Values

Destructure props in the function signature for cleaner code. Use default values for optional props to make components more robust and self-documenting.

**Incorrect (no destructuring, no defaults):**

```tsx
function Avatar(props) {
  return (
    <img
      src={props.person.imageUrl}
      alt={props.person.name}
      width={props.size}  // undefined if not passed
      height={props.size}
    />
  )
}
```

**Correct (destructuring with defaults):**

```tsx
interface AvatarProps {
  person: {
    name: string
    imageUrl: string
  }
  size?: number  // optional
}

function Avatar({ person, size = 100 }: AvatarProps) {
  return (
    <img
      src={person.imageUrl}
      alt={person.name}
      width={size}   // defaults to 100
      height={size}
    />
  )
}
```

**Advanced patterns:**

```tsx
// Destructuring nested props
function Card({ user: { name, age }, isActive = false }: CardProps) {
  return (
    <div className={isActive ? 'active' : ''}>
      {name} - {age}
    </div>
  )
}

// Rest props for pass-through
function Button({ variant, children, ...rest }: ButtonProps) {
  return (
    <button className={`btn btn-${variant}`} {...rest}>
      {children}
    </button>
  )
}
```

**Benefits:**

1. Clear what props a component expects
2. Default values make optional props explicit
3. Cleaner code without repetitive `props.`
4. TypeScript can infer types better

Reference: [Passing Props to a Component](https://react.dev/learn/passing-props-to-a-component)
