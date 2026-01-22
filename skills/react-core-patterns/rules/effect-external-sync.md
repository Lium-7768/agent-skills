# effect-external-sync

**Impact: MEDIUM (proper use of Effects)**

Effects are for synchronizing React with external systems—systems outside of React's control.

## Purpose of Effects

Use Effects to connect to:
- Browser APIs (document title, media queries, Intersection Observer)
- Network (WebSocket connections, Server-Sent Events)
- Third-party libraries (maps, charts, video players)
- Hardware (geolocation, device orientation)

## Correct Examples

```jsx
// ✅ Good: Syncing document title with React state
function Page({ title }) {
  useEffect(() => {
    document.title = title;
  }, [title]);
}

// ✅ Good: Setting up a WebSocket subscription
function ChatRoom({ roomId }) {
  const [messages, setMessages] = useState([]);
  
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on('message', msg => {
      setMessages(prev => [...prev, msg]);
    });
    return () => connection.disconnect();
  }, [roomId]);
}

// ✅ Good: Controlling a non-React video player
function VideoPlayer({ src, isPlaying }) {
  const ref = useRef(null);
  
  useEffect(() => {
    if (isPlaying) {
      ref.current.play();
    } else {
      ref.current.pause();
    }
  }, [isPlaying]);
  
  return <video ref={ref} src={src} />;
}

// ✅ Good: Measuring DOM elements
function Tooltip({ children }) {
  const ref = useRef(null);
  const [height, setHeight] = useState(0);
  
  useEffect(() => {
    setHeight(ref.current.getBoundingClientRect().height);
  });
  
  return <div ref={ref}>{children}</div>;
}
```

## Effect Cleanup

Always clean up side effects to prevent memory leaks:

```jsx
// ✅ Good: Cleanup subscription
useEffect(() => {
  const handler = () => console.log('resize');
  window.addEventListener('resize', handler);
  return () => window.removeEventListener('resize', handler);
}, []);

// ✅ Good: Cleanup timer
useEffect(() => {
  const id = setInterval(() => tick(), 1000);
  return () => clearInterval(id);
}, []);

// ✅ Good: Cancel fetch on unmount
useEffect(() => {
  let ignore = false;
  fetchData().then(data => {
    if (!ignore) setData(data);
  });
  return () => { ignore = true; };
}, []);
```

## References

- [Synchronizing with Effects](https://react.dev/learn/synchronizing-with-effects)
- [Lifecycle of reactive effects](https://react.dev/learn/lifecycle-of-reactive-effects)
