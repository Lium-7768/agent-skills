# a11y-semantic-html

**Impact: MEDIUM (accessibility and SEO)**

Use semantic HTML elements in React to make applications accessible to all users, including those using screen readers and keyboard navigation.

## Why This Matters

Semantic HTML provides:
- Built-in accessibility features
- Keyboard navigation
- Screen reader support
- Better SEO
- Consistent behavior across browsers

## Incorrect Examples

```jsx
// ❌ Bad: div for clickable element
function Bad() {
  return (
    <div onClick={handleClick} style={{ cursor: 'pointer' }}>
      Click me
    </div>
  );
}

// ❌ Bad: div for navigation
function BadNav() {
  return (
    <div className="nav">
      <div onClick={() => navigate('/')}>Home</div>
      <div onClick={() => navigate('/about')}>About</div>
    </div>
  );
}

// ❌ Bad: div for form
function BadForm() {
  return (
    <div>
      <div>Email</div>
      <input type="text" />
      <div onClick={submit}>Submit</div>
    </div>
  );
}
```

## Correct Examples

```jsx
// ✅ Good: button for clickable element
function Good() {
  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}

// ✅ Good: semantic navigation
function GoodNav() {
  return (
    <nav>
      <a href="/">Home</a>
      <a href="/about">About</a>
    </nav>
  );
}

// ✅ Good: semantic form
function GoodForm() {
  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="email">Email</label>
      <input id="email" type="email" />
      <button type="submit">Submit</button>
    </form>
  );
}
```

## Common Semantic Replacements

| Instead of | Use |
|------------|-----|
| `<div onClick>` | `<button>` |
| `<div>` for navigation | `<nav>` |
| `<div>` for main content | `<main>` |
| `<div>` for sidebar | `<aside>` |
| `<div>` for header | `<header>` |
| `<div>` for footer | `<footer>` |
| `<div>` for article | `<article>` |
| `<div>` for section | `<section>` |
| Text without label | `<label htmlFor="...">` |

## Accessibility Attributes in React

```jsx
// Labels for form elements
<label htmlFor="email">Email</label>
<input id="email" type="email" />

// ARIA for custom components
<div 
  role="button" 
  tabIndex={0} 
  aria-pressed={isPressed}
  onClick={handleClick}
  onKeyDown={handleKeyDown}
>
  Toggle
</div>

// Alt text for images
<img src={logo} alt="Company Logo" />
<img src={decorative} alt="" /> {/* Empty alt for decorative */}

// Hidden from screen readers
<span aria-hidden="true">🎉</span>
```

## Focus Management

```jsx
// Focus input on mount
function SearchModal({ isOpen }) {
  const inputRef = useRef(null);
  
  useEffect(() => {
    if (isOpen) {
      inputRef.current?.focus();
    }
  }, [isOpen]);
  
  return <input ref={inputRef} />;
}

// Skip to main content link
function Layout() {
  return (
    <>
      <a href="#main" className="skip-link">
        Skip to main content
      </a>
      <nav>...</nav>
      <main id="main">...</main>
    </>
  );
}
```

## References

- [Web Accessibility Initiative (WAI)](https://www.w3.org/WAI/)
- [React Accessibility Docs](https://react.dev/reference/react-dom/components#form-components)
