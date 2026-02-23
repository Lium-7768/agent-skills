---
name: react-core-patterns
description: React official best practices from react.dev documentation. Essential rules for writing correct, predictable React code. Covers Rules of Hooks, component purity, state management, avoiding unnecessary effects, and accessibility. Use when writing, reviewing, or fixing React components.
license: MIT
metadata:
  author: React Team / react.dev
  version: "1.0.0"
---

# React Core Patterns

Essential React best practices based on official React documentation. These are fundamental rules that ensure your React code is correct, predictable, and maintainable.

## When to Apply

Reference these guidelines when:
- Writing any React components or custom Hooks
- Reviewing React code for correctness
- Debugging unexpected React behavior
- Refactoring React code to follow best practices

## Rule Categories

| Priority | Category | Impact | Prefix |
|----------|----------|--------|--------|
| 1 | Rules of Hooks | CRITICAL | `hooks-` |
| 2 | React Calling Patterns | CRITICAL | `component-` |
| 3 | Component Purity | CRITICAL | `purity-` |
| 4 | Effect Best Practices | HIGH | `effect-` |
| 5 | State Structure | HIGH | `state-` |
| 6 | Rendering Patterns | MEDIUM | `rendering-` |
| 7 | Key Props | MEDIUM | `key-` |
| 8 | Accessibility | MEDIUM | `a11y-` |

## Quick Reference

### 1. Rules of Hooks (CRITICAL)

- `hooks-top-level` - Only call Hooks at the top level, never in loops/conditions/nested functions
- `hooks-react-functions` - Only call Hooks from React function components or custom Hooks
- `hooks-never-pass-as-value` - Never pass Hooks around as regular values

### 2. React Calling Patterns (CRITICAL)

- `component-never-call-directly` - Never call component functions directly, use JSX

### 3. Component Purity (CRITICAL)

- `purity-idempotent` - Components must return same output for same inputs
- `purity-side-effects` - Side effects must run outside of render (use Effects/event handlers)
- `purity-immutable` - Never mutate props, state, or values passed to JSX

### 4. Effect Best Practices (HIGH)

- `effect-avoid-unnecessary` - Don't use Effects to transform data or handle user events
- `effect-derive-state` - Calculate derived values during render, not in Effects
- `effect-external-sync` - Use Effects only for synchronizing with external systems

### 5. State Structure (HIGH)

- `state-group-related` - Group related state variables that always change together
- `state-avoid-redundant` - Don't store values that can be calculated from existing state/props
- `state-avoid-duplication` - Don't duplicate data across multiple state variables
- `state-avoid-nested` - Prefer flat state over deeply nested structures

### 6. Rendering Patterns (MEDIUM)

- `rendering-logical-and-pitfall` - Don't put numbers on left side of && (renders 0)

### 7. Key Props (MEDIUM)

- `key-unique-stable` - Use unique, stable keys for list items (not array index for dynamic lists)

### 8. Accessibility (MEDIUM)

- `a11y-semantic-html` - Use semantic HTML elements for proper accessibility

## How to Use

Read individual rule files for detailed explanations and code examples:

```
rules/hooks-top-level.md
rules/purity-idempotent.md
rules/effect-avoid-unnecessary.md
```

Each rule file contains:
- Brief explanation of why it matters
- Incorrect code example
- Correct code example
- References to official React documentation

## Full Compiled Document

For the complete guide with all rules expanded: `AGENTS.md`
