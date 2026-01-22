# effect-derive-state

**Impact: HIGH (performance and bug prevention)**

Don't store derived data in state synced via Effects. Calculate derived values during render instead.

## Why This Matters

Storing derived data in state:
1. Causes extra renders (state change → render → Effect → state change → render)
2. Creates sync bugs when you forget to update all related state
3. Makes code more complex than necessary

## Incorrect Examples

```jsx
// ❌ Bad: Derived fullName stored in state
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  const [fullName, setFullName] = useState('');
  
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  
  return (
    <>
      <input value={firstName} onChange={e => setFirstName(e.target.value)} />
      <input value={lastName} onChange={e => setLastName(e.target.value)} />
      <p>Hello, {fullName}</p>
    </>
  );
}

// ❌ Bad: Filtered list in state
function ProductList({ products }) {
  const [search, setSearch] = useState('');
  const [filtered, setFiltered] = useState(products);
  
  useEffect(() => {
    setFiltered(products.filter(p => 
      p.name.toLowerCase().includes(search.toLowerCase())
    ));
  }, [products, search]);
}
```

## Correct Examples

```jsx
// ✅ Good: Calculate during render
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');
  
  // Derived value - no state needed!
  const fullName = firstName + ' ' + lastName;
  
  return (
    <>
      <input value={firstName} onChange={e => setFirstName(e.target.value)} />
      <input value={lastName} onChange={e => setLastName(e.target.value)} />
      <p>Hello, {fullName}</p>
    </>
  );
}

// ✅ Good: Calculate during render (with memoization if expensive)
function ProductList({ products }) {
  const [search, setSearch] = useState('');
  
  // Derived value
  const filtered = products.filter(p => 
    p.name.toLowerCase().includes(search.toLowerCase())
  );
  
  // Or with useMemo if filtering is expensive:
  // const filtered = useMemo(() => 
  //   products.filter(p => p.name.toLowerCase().includes(search.toLowerCase())),
  //   [products, search]
  // );
}
```

## When to Use useMemo

Only memoize when the calculation is actually expensive:
- Filtering/sorting large arrays (1000+ items)
- Complex computations
- Creating objects passed to memoized children

## References

- [Updating state based on props or state](https://react.dev/learn/you-might-not-need-an-effect#updating-state-based-on-props-or-state)
- [Caching expensive calculations](https://react.dev/learn/you-might-not-need-an-effect#caching-expensive-calculations)
