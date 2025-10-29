# React Fiber

React Fiber is a complete rewrite of React’s core reconciliation engine — the part responsible for rendering and updating the UI.  
It was introduced in React 16 to solve a key problem: **Reconciliation was too synchronous.**

---

## Why Fiber Exists

Before Fiber, React’s rendering was a **recursive, synchronous process**.  
Once it started reconciling the component tree, it couldn’t pause until the entire tree was processed.  
For large trees, this could block the main thread and lead to dropped frames or janky interactions.

The Fiber architecture reimagines reconciliation as an **incremental, interruptible process**.  
Instead of a single stack-based recursion, React uses a linked data structure to represent the component tree.  
This makes rendering work **resumable, pausable, and prioritizable**.

---

## The Core Idea

A *fiber* is a lightweight unit of work — a node that represents one React element instance.  
Each fiber holds information like:

- The component type (function, class, host, etc.)
- The pending props and state  
- The child, sibling, and parent relationships  
- The work that needs to be done (update, mount, delete)
- The effect list (side effects to commit later)

In essence, the Fiber tree is React’s **internal runtime representation** of your component tree.

---

## From Recursion to Linked List

The previous stack-based reconciler relied on the JavaScript call stack.  
React Fiber replaces that with its own **linked list of units of work**.  

This gives React full control over how and when to perform work — it can pause between units, yield control back to the browser, and resume later.  
This is what enables features like **Concurrent Rendering** and **Suspense**.

---

## The Render and Commit Phases

React Fiber breaks the update process into two distinct phases:

1. **Render Phase (Reconciliation)**  
   - Work is performed on the Fiber tree to determine what changes are needed.  
   - This phase is *interruptible* and can be paused or resumed.  
   - The output is a list of effects (insertions, updates, deletions).

2. **Commit Phase**  
   - The effect list is applied to the DOM.  
   - This phase is *synchronous* — once started, it must finish.

---

## Scheduling and Prioritization

Fiber’s most important feature is **time slicing** — the ability to break work into chunks and spread it over multiple frames.  
React assigns a **priority level** to each update (e.g., user input, animation, background data load).  
High-priority work can interrupt lower-priority rendering, ensuring smoother interactions.

This mechanism is managed by the **Scheduler**, which decides what unit of work to process next based on deadlines and priorities.

---

## Summary

React Fiber turned reconciliation from a recursive algorithm into a cooperative scheduler.  
It made React rendering **asynchronous, incremental, and interruptible** — setting the foundation for features like:

- Concurrent Mode  
- Suspense  
- Transitions  

It’s not just a new data structure — it’s a new *execution model* for React.

---

Next: **The Fiber Tree and Effect Tags**
