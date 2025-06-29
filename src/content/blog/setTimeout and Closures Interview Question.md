---
title: "setTimeout and Closures: The Classic JavaScript Interview Puzzle"
description: "A deep dive into the classic JavaScript interview puzzle involving setTimeout and closures, exploring why it behaves unexpectedly with var in loops, and how to fix it using let, IIFEs, and bind."
publishDate: 2024-01-19
tags: ["javascript", "interview", "closures", "setTimeout", "asynchronous", "best-practices"]
---

A classic JavaScript interview puzzle combines **asynchronous timers** with **closures**, revealing surprising behavior when you mix `var` and looped callbacks. In this deep dive, we’ll dissect why these pitfalls occur, explore multiple fixes, and discuss how understanding the event loop and lexical scoping helps you avoid such traps in real-world code.

### 1. The Classic Interview Scenario

Consider this code:

```js
for (var i = 1; i <= 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, 100);
}
```

**What does it print?** At first glance, many expect:

```
1
2
3
```

But in fact, it prints:

```
4
4
4
```

Why **4**? After the loop ends, `i` has incremented past 3 to 4. Each callback’s closure holds a _reference_ to the single `i` variable. By the time the timers fire (after ~100 ms), the loop is done and `i === 4`, so every callback logs 4.

### 2. Under the Hood: Call Stack, Event Loop & Closures

1. **Loop execution** (synchronous)
    
    - `i` is declared with `var` (function-scoped) and incremented from 1 to 4.
        
    - Three calls to `setTimeout` schedule callbacks; each callback _closes over_ the same `i`.
        
2. **Timer callbacks fire** (asynchronous)
    
    - When the event loop picks them up, each callback runs in its own execution context but shares the _outer_ lexical environment where `i === 4`.
        

Understanding this flow is crucial: **closures** capture _variables_, not values, and **timers** defer execution until _after_ the current call stack is empty.

### 3. Fix 1: Use `let` for Block Scoping

ES6’s `let` creates a fresh binding per iteration:

```js
for (let i = 1; i <= 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, 100);
}
```

Here, each loop iteration has its own `i`. When the callbacks run, they each see their own copy: `1`, then `2`, then `3`.

### 4. Fix 2: Capture the Value with an IIFE

Before `let`, developers used Immediately Invoked Function Expressions to create a new scope:

```js
for (var i = 1; i <= 3; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j);
    }, 100);
  })(i);
}
```

- The IIFE takes the current `i` as parameter `j`, which is block-scoped within that invocation.
    
- Each callback closes over its own `j`, preserving the value 1, 2, and 3 respectively.
    

### 5. Fix 3: Use `Function.prototype.bind`

You can also bind the current value to the callback’s `this` or to its first argument:

```js
for (var i = 1; i <= 3; i++) {
  setTimeout(
    console.log.bind(null, i), // binds i as the first argument
    100
  );
}
```

- `bind(null, i)` returns a new function that, when called, invokes `console.log(i)`.
    
- Each bound function captures its own `i`.
    

### 6. Advanced Example: Delays Based on Iteration

What if you want increasing delays?

```js
for (let i = 1; i <= 3; i++) {
  setTimeout(() => console.log(i), i * 100);
}
```

Output:

```
1  (after 100 ms)
2  (after 200 ms)
3  (after 300 ms)
```

With `let`, you get both correct values and staggered timings.

### 7. Pitfalls Beyond `var` Loops

Closures + async callbacks can surprise in other contexts:

```js
function attachHandlers(buttons) {
  for (var i = 0; i < buttons.length; i++) {
    buttons[i].addEventListener('click', function() {
      console.log('Button index:', i);
    });
  }
}
```

Every click logs the final `i` (buttons.length), not the index at binding time. The same fixes apply: use `let`, IIFE, or bind.

### 8. Best Practices

1. **Prefer `let`/`const`** for block-level loops and variables.
    
2. **Minimize shared mutable state** in closures—capture only what you need.
    
3. **Use arrow functions** to keep callbacks concise, but still pair with `let`.
    
4. **Test edge cases**: loops, dynamic handler attachment, and nested async calls.
    
5. **Document intent** when using older patterns (IIFEs/bind) so teammates understand why you captured values.
    

---
