# key-unique-stable

**Impact: MEDIUM (prevents re-render bugs and state loss)**

Always use unique, stable keys for list items. Never use array index as key for dynamic lists.

## Why This Matters

React uses keys to match elements between renders. Wrong keys cause:
- Incorrect state preservation (input values in wrong rows)
- Performance issues (unnecessary re-mounts)
- Animation bugs

## Incorrect Examples

```jsx
// ❌ Bad: Using array index for dynamic list
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map((todo, index) => (
        <TodoItem key={index} todo={todo} />  // Bug if list reorders!
      ))}
    </ul>
  );
}

// ❌ Bad: Generating key during render
function ItemList({ items }) {
  return (
    <ul>
      {items.map(item => (
        <Item key={Math.random()} item={item} />  // New key every render!
      ))}
    </ul>
  );
}

// ❌ Bad: Non-unique keys
function CommentList({ comments }) {
  return (
    <ul>
      {comments.map(comment => (
        <Comment key={comment.author} comment={comment} />  // May not be unique!
      ))}
    </ul>
  );
}
```

## Correct Examples

```jsx
// ✅ Good: Using unique ID from data
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}

// ✅ Good: Composite key when no single unique field
function ContactList({ contacts }) {
  return (
    <ul>
      {contacts.map(contact => (
        <Contact key={`${contact.email}-${contact.phone}`} contact={contact} />
      ))}
    </ul>
  );
}

// ✅ OK: Index is fine for static lists that never reorder
function StaticNavigation() {
  const links = ['Home', 'About', 'Contact']; // Never changes
  return (
    <nav>
      {links.map((link, index) => (
        <a key={index} href={`/${link.toLowerCase()}`}>{link}</a>
      ))}
    </nav>
  );
}
```

## When Index is OK

Array index as key is acceptable ONLY when ALL of these are true:
- List is static (no additions/removals)
- List never reorders
- Items have no state or inputs

## Using Key to Reset Component State

Keys can reset component state when they change:

```jsx
// Reset form when user changes
function App({ userId }) {
  return <ProfileForm key={userId} userId={userId} />;
}
// When userId changes, ProfileForm unmounts and remounts fresh
```

## References

- [Rendering Lists](https://react.dev/learn/rendering-lists#keeping-list-items-in-order-with-key)
- [Preserving and Resetting State](https://react.dev/learn/preserving-and-resetting-state#resetting-state-with-a-key)
