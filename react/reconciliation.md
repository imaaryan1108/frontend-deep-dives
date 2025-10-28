# React Reconciliation

Reconciliation is React’s internal process of figuring out **what changed in the UI** and updating the real DOM efficiently.  
It’s how React keeps your app fast and predictable — even as state and props change.

---

## Why Reconciliation Exists

In React, you don’t manipulate the DOM directly.  
You describe *what the UI should look like*, and React figures out how to get there.

When state or props change, React needs to:
1. Compare the previous Virtual DOM with the new one.
2. Find the minimal difference (the “diff”).
3. Apply only those changes to the real DOM.

This entire process is called **Reconciliation**.

---

## How Reconciliation Works

Every time you render a React component, React creates a new Virtual DOM tree.  
Then it compares this new tree with the previous one.

### The Three-Step Process

1. **Render Phase** → React builds a new Virtual DOM tree based on new state/props.  
2. **Diff Phase** → React compares the new tree with the old one (using keys + heuristics).  
3. **Commit Phase** → React applies only the necessary changes to the real DOM.

---

## Diffing Algorithm (Simplified)

React uses a highly optimized O(n) algorithm with two main assumptions:

1. **Different types → different trees**
   ```jsx
   <div> → <span> // Entire subtree replaced
   ```

2. **Stable keys help React track elements in lists**
   ```jsx
   items.map((item) => <li key={item.id}>{item.name}</li>);
   ```

If a key changes, React treats it as a *new element* — unmounting the old one and mounting a new one.

Example:
```jsx
<ul>
  <li key="a">A</li>
  <li key="b">B</li>
</ul>

// becomes

<ul>
  <li key="b">B</li>
  <li key="a">A</li>
</ul>
```
React won’t reorder these; it’ll destroy and recreate both `<li>`s unless keys are stable.

---

## How React Differs Nodes

React walks the two trees (old and new) at the same time:

1. If both nodes are of the **same type**, React compares their attributes (props).  
2. If attributes differ, React updates them in the real DOM.  
3. If node types differ, React destroys the old subtree and mounts a new one.

It’s a **depth-first** comparison — React checks child components recursively.

---

## Keys — The Backbone of Reconciliation

Keys help React **match** elements between renders.

Without keys:
```jsx
{list.map((item) => <li>{item}</li>)}
```
React only matches by index → unexpected UI when order changes.

With keys:
```jsx
{list.map((item) => <li key={item.id}>{item.name}</li>)}
```
React knows which node corresponds to which data — updates are precise and minimal.

Keys should be:
- **Stable:** Doesn’t change between renders.  
- **Unique:** Within the same list.  
- **Predictable:** Derived from data, not array index.

---

## Example

```jsx
function List() {
  const [items, setItems] = React.useState(["A", "B", "C"]);

  return (
    <div>
      {items.map((item) => (
        <p key={item}>{item}</p>
      ))}
      <button onClick={() => setItems(["C", "B", "A"])}>Reverse</button>
    </div>
  );
}
```

With keys: React reuses `<p>` nodes, just swaps text.  
Without keys: React destroys and recreates `<p>` elements.

---

## Reconciliation and Components

React treats components as “units of UI.”  
When the component type stays the same, React reuses the existing instance and just updates its props.

```jsx
<Profile name="Alex" /> → <Profile name="Sam" />
```
✅ React reuses the same component instance; only updates props.

But:
```jsx
<Profile /> → <UserProfile />
```
❌ React unmounts `Profile` and mounts a new `UserProfile`.

---

## When Reconciliation Happens

- Every time `setState()` or `useState()` updates.  
- When parent props change.  
- When context values update.  

React batches these updates to avoid unnecessary re-renders.

---

## Common Misconceptions

❌ “Reconciliation means re-rendering the whole DOM.”  
→ React only diffs virtual nodes — the DOM changes are minimal.

❌ “Keys improve performance.”  
→ Keys improve *correctness*. Performance is a side effect.

❌ “React re-renders everything on every state change.”  
→ React re-renders components whose data changed, but may skip others using memoization.

---

## Internal Note: Fiber and Reconciliation

React’s diffing logic is implemented within the **Fiber tree** (React 16+).  
Each node in the Fiber tree represents a unit of work (component + state + effect).  
The reconciliation algorithm traverses this Fiber tree and marks updates during the “Render Phase.”

Actual DOM mutations happen later in the **Commit Phase.**

---

## Interview Notes

- Reconciliation → comparing old and new trees to update the DOM efficiently.  
- Diffing → uses heuristics (type + keys) to minimize work.  
- Keys → help React preserve component identity.  
- Fiber → under-the-hood structure that powers reconciliation.  
- Commit Phase → where React applies real DOM changes.

---

## Summary

- React’s reconciliation is a **diffing strategy**, not a full re-render.  
- The **Virtual DOM** enables fast comparisons in memory.  
- Keys are essential to maintain consistency and prevent unnecessary re-renders.  
- With Fiber, reconciliation is now **incremental, interruptible, and prioritized.**  

Understanding reconciliation is understanding *how React actually updates the UI*.  
It’s the bridge between your `setState()` and what users see.
