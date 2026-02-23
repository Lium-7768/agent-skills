# state-avoid-nested

**Impact: MEDIUM (cleaner update logic)**

Avoid deeply nested state structures. Flatten state where possible for easier updates.

## Why This Matters

Deeply nested state is hard to update immutably. You end up with complex spread operations that are error-prone and hard to read.

## Incorrect Examples

```jsx
// ❌ Bad: Deeply nested state
const [state, setState] = useState({
  user: {
    profile: {
      settings: {
        theme: 'dark',
        notifications: {
          email: true,
          push: false
        }
      }
    }
  }
});

// Updating is painful:
function togglePush() {
  setState({
    ...state,
    user: {
      ...state.user,
      profile: {
        ...state.user.profile,
        settings: {
          ...state.user.profile.settings,
          notifications: {
            ...state.user.profile.settings.notifications,
            push: !state.user.profile.settings.notifications.push
          }
        }
      }
    }
  });
}

// ❌ Bad: Nested tree structure
const [places, setPlaces] = useState({
  id: 0,
  title: 'Earth',
  children: [{
    id: 1,
    title: 'Europe',
    children: [{
      id: 2,
      title: 'France'
    }]
  }]
});
```

## Correct Examples

```jsx
// ✅ Good: Flat state
const [theme, setTheme] = useState('dark');
const [emailNotifications, setEmailNotifications] = useState(true);
const [pushNotifications, setPushNotifications] = useState(false);

// Simple updates:
function togglePush() {
  setPushNotifications(prev => !prev);
}

// ✅ Good: Normalized/flat tree structure
const [places, setPlaces] = useState({
  0: { id: 0, title: 'Earth', childIds: [1] },
  1: { id: 1, title: 'Europe', childIds: [2] },
  2: { id: 2, title: 'France', childIds: [] }
});
const [rootId] = useState(0);

// Updates are simpler:
function renamePlace(id, newTitle) {
  setPlaces({
    ...places,
    [id]: { ...places[id], title: newTitle }
  });
}
```

## Normalization Pattern

For hierarchical data like trees:

```jsx
// ❌ Nested (hard to update)
{
  id: 1,
  children: [{ id: 2, children: [...] }]
}

// ✅ Normalized (easy to update)
{
  1: { id: 1, childIds: [2] },
  2: { id: 2, childIds: [] }
}
```

## When Nesting is OK

- Read-only data (no updates needed)
- Simple two-level structures
- Data from server you'll replace entirely

## References

- [Avoid deeply nested state](https://react.dev/learn/choosing-the-state-structure#avoid-deeply-nested-state)
