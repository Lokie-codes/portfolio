---
title: "Block Scope & Shadowing in JavaScript"
description: "Explore how block scope and shadowing work in JavaScript, including best practices to avoid common pitfalls."
publishDate: 2025-01-07
tags: ["javascript", "block-scope", "shadowing", "es6"]
---
In JavaScript, **block scope** allows you to confine variables to the nearest set of curly braces (`{ … }`), while **shadowing** lets an inner scope declare a name that hides (or “shadows”) a binding in an outer scope. Together, these features give you fine-grained control over variable visibility and lifetime—when used thoughtfully, they help prevent accidental name collisions and unintended side-effects. Let’s explore how block scope works, examine shadowing scenarios, and review best practices.

### 1. Block Scope: The Basics

Prior to ES6, JavaScript only offered function-level scope via `var`. Any `var` declared inside a block was effectively hoisted to the top of its enclosing function:

```js
function legacyLoop() {
  if (true) {
    var i = 42;
  }
  console.log(i);    // 42 — `i` lives throughout the function
}
legacyLoop();
```

With ES6’s `let` and `const`, variables gain **block scope**—they exist only between the `{` and `}` in which they’re defined:

```js
function modernLoop() {
  if (true) {
    let j = 99;
    const PI = 3.14;
  }
  console.log(j);    // ReferenceError: j is not defined
  console.log(PI);   // ReferenceError: PI is not defined
}
modernLoop();
```

- **`let`** creates a mutable, block-scoped binding.
    
- **`const`** creates an immutable, block-scoped binding (the binding itself is immutable; object contents may still change).
    

### 2. The Temporal Dead Zone in Blocks

Every `let` or `const` variable enters a **Temporal Dead Zone** (TDZ) from the start of its block until its declaration executes. During this zone, any access throws a ReferenceError:

```js
{
  // TDZ for `msg` begins
  // console.log(msg); // ReferenceError: Cannot access 'msg' before initialization

  let msg = 'Hello';
  console.log(msg);    // 'Hello'
}
```

This behavior prevents accessing an uninitialized binding and encourages you to declare variables before you use them.

### 3. Shadowing: Hiding Outer Bindings

**Shadowing** occurs when an inner scope declares a variable with the same name as one in an outer scope. The inner binding **shadows** the outer one for the duration of its block:

```js
let count = 1;

{
  let count = 2;            // shadows the outer `count`
  console.log(count);       // 2
}

console.log(count);         // 1 — outer `count` remains unchanged
```

Shadowing can be useful—for example, in loops or nested functions—but overuse can lead to confusion. Always choose descriptive names to minimize accidental shadowing.

### 4. Examples of Shadowing in Nested Blocks

#### 4.1 Loop-Level Shadowing

```js
const arr = [10, 20, 30];

for (let index = 0; index < arr.length; index++) {
  let value = arr[index];
  console.log(index, value);
}

// `index` and `value` no longer exist here
// console.log(index); // ReferenceError
```

Using `let` for loop counters ensures each iteration’s variables do not leak outside the loop.

#### 4.2 Function vs. Block Shadowing

```js
let name = 'Global';

function greet() {
  let name = 'Local';        // shadows the global `name`
  console.log(`Hello, ${name}`);
}

greet();                     // 'Hello, Local'
console.log(name);           // 'Global'
```

Here, the function’s block creates a fresh `name`, leaving the global binding intact.

#### 4.3 Nested Shadowing

```js
let x = 1;

function outer() {
  let x = 2;                 // shadows global x
  {
    let x = 3;               // shadows outer’s x
    console.log(x);          // 3
  }
  console.log(x);            // 2
}

outer();
console.log(x);              // 1
```

Each nested block introduces its own `x`, resolved by the block’s scope first, then moving outward.

### 5. Block Scope with `const` and Immutable Patterns

While `const` prevents rebinding, you can still shadow a `const` in an inner block:

```js
const CONFIG = { mode: 'prod' };

{
  const CONFIG = { mode: 'dev' };    // shadows outer CONFIG
  console.log(CONFIG.mode);          // 'dev'
}

console.log(CONFIG.mode);            // 'prod'
```

This pattern lets you override configuration in local contexts without affecting the global constant.

### 6. Pitfalls and Gotchas

- **Over‐shadowing**: Reusing the same name multiple times can make it hard to track which binding you’re referring to.
    
- **Confusing TDZ errors**: Attempting to read a shadowed `let` before its declaration within the same block triggers a ReferenceError.
    
- **`var` + blocks**: Mixing `var` and block-scoped declarations can lead to unexpected leaks:
    
    ```js
    if (true) {
      var a = 'foo';
      let b = 'bar';
    }
    console.log(a); // 'foo'
    console.log(b); // ReferenceError
    ```
    

### 7. Best Practices for Block Scope & Shadowing

1. **Favor `const` by Default**  
    Use `const` for every binding you don’t intend to reassign; switch to `let` only when reassignment is required.
    
2. **Declare Variables Close to Their Use**  
    Keeping declarations near their first usage reduces the TDZ window and clarifies intent.
    
3. **Use Descriptive Names**  
    Avoid shadowing by choosing unique, meaningful names—especially for widely-scoped bindings.
    
4. **Limit Block Nesting**  
    Deeply nested blocks can create many shadowed layers; flatten your code when possible for readability.
    
5. **Lint for Shadowing**  
    Enable ESLint’s [`no-shadow`](https://eslint.org/docs/rules/no-shadow) rule to catch accidental shadowing.
    
6. **Avoid Mixing `var` with `let`/`const`**  
    Prefer ES6 declarations exclusively to prevent `var`’s hoisting and leakage from undermining block scope.
    

---
