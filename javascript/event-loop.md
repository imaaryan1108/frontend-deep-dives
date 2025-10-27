# The Event Loop

JavaScript is single-threaded — it can only do one thing at a time.  
Yet somehow it handles timers, promises, user clicks, and async calls all at once.  
That magic is the **Event Loop**.

---

## The Problem It Solves

If JavaScript blocked the thread for every network request or timeout,  
the browser would freeze constantly.

The event loop solves this by managing *when* callbacks run,  
so JS can continue executing other work while waiting for tasks to complete.

---

## The Main Players

### 1. Call Stack
This is where your synchronous code runs.  
Each function call adds a frame to the stack.  
When it’s done, that frame is popped off.

Example:
```js
function first() {
  console.log("First");
}
function second() {
  first();
  console.log("Second");
}
second();
```
Stack trace:
```
> second()
  > first()
```
Output:
```
First
Second
```

---

### 2. Web APIs
Browsers and Node provide background workers — not part of JS itself.

These include:
- `setTimeout`, `setInterval`
- `fetch`, `XMLHttpRequest`
- `DOM events`
- `Promises` (microtasks handled differently)

They handle async work *outside* the main thread.

---

### 3. Task Queues

After an async operation finishes,  
its callback is queued for execution when the stack is empty.

There are **two main queues**:
- **Macro Task Queue:** for tasks like `setTimeout`, `setInterval`, `setImmediate`.  
- **Micro Task Queue:** for Promises, `queueMicrotask`, `process.nextTick`.

Microtasks always run **before** macrotasks once the stack is clear.

---

## The Event Loop in Action

Let’s visualize:

```js
console.log("Start");

setTimeout(() => console.log("Timeout"), 0);

Promise.resolve()
  .then(() => console.log("Promise 1"))
  .then(() => console.log("Promise 2"));

console.log("End");
```

Output:
```
Start
End
Promise 1
Promise 2
Timeout
```

### Why?

- Stack runs `console.log("Start")` → `setTimeout(...)` → `Promise.resolve(...)` → `console.log("End")`.  
- Stack empties.  
- Event loop checks **microtasks first** → runs `.then()` handlers.  
- Only after that does it move to the **macrotask queue** (the `setTimeout` callback).

---

## Diagram

```
     ┌─────────────────────────────┐
     │        Call Stack           │
     └────────────┬────────────────┘
                  │
                  ▼
          ┌──────────────┐
          │   Web APIs   │
          └──────────────┘
                  │
        ┌─────────┴─────────┐
        │                   │
        ▼                   ▼
   Microtask Queue     Macrotask Queue
 (Promises, nextTick)  (Timeouts, I/O)
        │                   │
        └───────┬───────────┘
                ▼
          Event Loop
         (checks if stack empty)
```

---

## Key Concepts

### Microtasks > Macrotasks
The microtask queue has higher priority.  
After every synchronous phase, all pending microtasks run *before* any macrotasks.

This is why:
```js
setTimeout(() => console.log("timeout"), 0);
Promise.resolve().then(() => console.log("promise"));
```
logs:
```
promise
timeout
```

---

### Infinite Microtasks = Browser Freeze

If a microtask enqueues another microtask,  
it can block the loop forever:

```js
Promise.resolve().then(function repeat() {
  Promise.resolve().then(repeat);
});
```
This prevents control from ever returning to the macrotask queue — the UI hangs.

---

## Node.js Event Loop (extra credit)

Node’s event loop is built on **libuv**.  
It has more phases than the browser:
1. **Timers** → `setTimeout`, `setInterval`
2. **Pending callbacks**
3. **Idle / prepare**
4. **Poll** → I/O
5. **Check** → `setImmediate`
6. **Close callbacks**

Microtasks (like Promises) run **between** these phases — same rule: before moving on.

---

## Interview Notes

- JS is **single-threaded**; async behavior comes from Web APIs.  
- Event loop → bridges sync (stack) and async (queues).  
- **Microtasks run before macrotasks.**  
- Common example: `Promise` vs `setTimeout` order.  
- Node.js loop adds multiple internal phases (libuv).

---

## Summary

The event loop is the reason JS feels asynchronous without actually running in parallel.  
It’s a smart scheduler that decides *when* to run callbacks once the main thread is free.

If you ever debug timing issues in Promises or `setTimeout`,  
remember: **it’s all just queues and priorities.**
