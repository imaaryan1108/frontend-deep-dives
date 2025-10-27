# JavaScript Engine

JavaScript runs inside an engine.  
Every browser and runtime has one — Chrome and Node.js use **V8**, Firefox has **SpiderMonkey**, Safari has **JavaScriptCore**.

The engine’s job is simple:  
**take JavaScript code and make it run fast.**

---

## How the Engine Runs Code

When you run a file like `index.js`, this is what happens under the hood:

1. **Parsing**  
   The engine reads your source code, breaks it into tokens, and builds an Abstract Syntax Tree (AST).  
   Example:

   ```js
   const a = 10;
   ```

   → becomes something like:

   ```
   VariableDeclaration
     Identifier (a)
     Literal (10)
   ```

2. **Compilation**  
   Modern JS engines don’t just interpret code — they compile it on the fly.  
   V8 uses:

   - **Ignition (interpreter)** → turns JS into bytecode and starts executing it immediately.
   - **Turbofan (compiler)** → watches for “hot” (frequently used) code and replaces it with optimized machine code.

   This hybrid approach is called **Just-In-Time (JIT)** compilation — the best of both worlds:  
   fast startup + optimized runtime.

3. **Execution**  
   Once compiled, your code runs inside the **call stack**.

   - Each function call creates an **execution context** (pushed to the stack).
   - Variables and values are stored either in the **stack** or the **heap**:
     - Stack → primitives and function calls (fast, fixed size).
     - Heap → objects, arrays, closures (dynamic, slower).

4. **Garbage Collection**  
   When data is no longer referenced, the garbage collector (GC) clears it out of the heap.

   V8 mainly uses the **Mark-and-Sweep** algorithm:

   - Start from global roots and mark everything reachable.
   - Sweep (delete) everything else.

   The old **reference counting** approach failed on circular references like:

   ```js
   const a = {};
   const b = {};
   a.ref = b;
   b.ref = a; // both stay referenced forever
   ```

---

## Example: What Actually Happens

```js
function add(a, b) {
  return a + b;
}

const result = add(5, 7);
```

Here’s the rough timeline:

1. Source code → tokenized → parsed → AST.
2. Ignition creates bytecode and runs it.
3. Turbofan sees `add()` is reused → optimizes it into machine code.
4. Call stack:
   - Push global context.
   - Push `add()` when called.
   - Execute and pop after return.
5. GC cleans up once `result` is stored and `add()`’s context is gone.

---

## Notes for Interviews

- **JIT Compilation** → Combines interpreter and compiler for both speed and optimization.
- **V8 Components** → Ignition (interpreter), Turbofan (compiler), Orinoco (garbage collector).
- **Memory Layout** → Stack = primitives, Heap = objects.
- **GC Algorithm** → Mark & Sweep (reachability-based).
- **Optimization Trigger** → Hot code paths detected during execution.

---

## Summary

JS execution in one line:  
**Parse → Compile → Execute → Clean Up**

V8 makes this process fast by mixing interpretation and compilation.  
It manages memory automatically, freeing up anything that’s unreachable.

If you understand this flow, you understand what really happens when you type `node app.js`.
