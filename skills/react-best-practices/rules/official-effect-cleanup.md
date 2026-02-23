---
title: Always Clean Up Effects
impact: HIGH
impactDescription: prevents memory leaks and resource issues
tags: react, hooks, useEffect, cleanup, memory-leaks
---

## Always Clean Up Effects

Effects often create resources that need cleanup (subscriptions, timers, connections). Return a cleanup function from useEffect to prevent memory leaks.

**Incorrect (no cleanup):**

```tsx
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(roomId)
    connection.connect()

    // ❌ No cleanup! Connection never closed
  }, [roomId])
}

function Timer() {
  useEffect(() => {
    setInterval(() => {
      console.log('tick')
    }, 1000)

    // ❌ Interval never cleared, keeps running
  }, [])
}
```

**Correct (with cleanup):**

```tsx
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(roomId)
    connection.connect()

    return () => {
      connection.disconnect()  // ✅ Clean up connection
    }
  }, [roomId])
}

function Timer() {
  useEffect(() => {
    const intervalId = setInterval(() => {
      console.log('tick')
    }, 1000)

    return () => {
      clearInterval(intervalId)  // ✅ Clear interval
    }
  }, [])
}
```

### Common Cleanup Patterns

```tsx
// Event listeners
useEffect(() => {
  const handleResize = () => setWidth(window.innerWidth)
  window.addEventListener('resize', handleResize)

  return () => {
    window.removeEventListener('resize', handleResize)  // ✅
  }
}, [])

// Subscriptions
useEffect(() => {
  const subscription = observable.subscribe(data => setData(data))

  return () => {
    subscription.unsubscribe()  // ✅
  }
}, [])

// WebSocket
useEffect(() => {
  const ws = new WebSocket(url)
  ws.onmessage = handleMessage

  return () => {
    ws.close()  // ✅
  }
}, [url])

// Document title restoration
useEffect(() => {
  const originalTitle = document.title
  document.title = `Chat: ${roomId}`

  return () => {
    document.title = originalTitle  // ✅
  }
}, [roomId])
```

### When Cleanup Runs

```tsx
useEffect(() => {
  console.log('Effect runs: setup')

  return () => {
    console.log('Cleanup runs')
  }
}, [dep])

// Cleanup runs:
// 1. Before effect runs again (when deps change)
// 2. Before component unmounts
```

Reference: [Synchronizing with Effects](https://react.dev/learn/synchronizing-with-effects)
