# Closures in JavaScript

Closures are one of those concepts that look simple but explain half of JavaScript’s power.  
They’re the reason functions “remember” variables even after the outer scope has gone.

---

## The Core Idea

A closure happens when a function **captures variables from its outer scope** —  
and keeps access to them even after that scope has finished executing.

In plain terms:  
> "A closure is a function that remembers where it came from."

Example:
```js
function outer() {
  const name = "Alex";
  function inner() {
    console.log(`Hello ${name}`);
  }
  return inner;
}

const greet = outer();
greet(); // Hello Alex
```

Even though `outer()` is done executing, `inner()` still remembers `name`.  
That’s the closure at work.

---

## Why Closures Exist

Each function in JS has a **lexical environment** — basically, a scope chain.

When a function is created, it keeps a hidden reference to the environment where it was defined.  
That reference is carried along with the function everywhere it goes.

So when you call a nested function later, JS can still look “up” its original scope chain to find variables.

This is called **lexical scoping** — determined by where the function is *written*, not where it’s *called*.

---

## Another Example

```js
function counter() {
  let count = 0;

  return function increment() {
    count++;
    console.log(count);
  };
}

const c1 = counter();
c1(); // 1
c1(); // 2

const c2 = counter();
c2(); // 1 (new closure, new memory space)
```

Each call to `counter()` creates a new lexical environment with its own `count`.  
The returned function holds on to that reference.

Closures allow **private variables** — values hidden from the outside world.

---

## Closures and Memory

Since a closure holds references to variables from its outer scope,  
those variables stay in memory as long as the closure exists.

Example:
```js
function outer() {
  const bigArray = new Array(1000000).fill("*");

  return function inner() {
    console.log("Still here");
  };
}

const fn = outer(); // bigArray is not GC'ed
```
Even though `outer()` finished, `bigArray` stays alive because `inner` still references it.

### Avoiding Memory Leaks

Once you’re done with a closure, you can free memory manually by setting it to `null`:
```js
fn = null; // allows GC to reclaim memory
```

---

## Common Closure Patterns

### 1. Function Factories
Creating preconfigured functions:
```js
function makeMultiplier(multiplier) {
  return function (num) {
    return num * multiplier;
  };
}

const double = makeMultiplier(2);
console.log(double(5)); // 10
```

### 2. Module Pattern
Using closures to simulate private variables:
```js
function createUser() {
  let name = "Anonymous";

  return {
    setName(newName) { name = newName; },
    greet() { console.log(`Hello ${name}`); }
  };
}

const user = createUser();
user.setName("Luna");
user.greet(); // Hello Luna
```

### 3. Event Handlers
Closures keep state inside callback functions:
```js
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000);
}
// 0, 1, 2
```

If you used `var`, all logs would print the same value because `var` is function-scoped — not block-scoped.  
Each `let` creates a new closure.

---

## Interview Questions Around Closures

### Q1. What is a closure?
> A closure is a function that has access to variables from its outer lexical scope, even after that scope has returned.

### Q2. Why are closures useful?
> For data privacy, encapsulation, and maintaining state between function calls.

### Q3. Can closures cause memory leaks?
> Yes — if large objects remain referenced inside a closure that’s never released.

### Q4. Difference between lexical scope and closure?
> Lexical scope is the *static structure* of variable access.  
> A closure is the *runtime phenomenon* that captures those variables.

---

## Summary

- A closure forms whenever a function is defined inside another function.  
- It gives the inner function access to the outer function’s scope.  
- Great for **encapsulation** and **stateful logic**.  
- Be mindful of **memory leaks** if closures hold large objects.

If you understand closures, you understand half of how JS “remembers” —  
and why it behaves the way it does with functions and callbacks.
