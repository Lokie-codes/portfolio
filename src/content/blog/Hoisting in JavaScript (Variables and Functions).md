---
# Hoisting in JavaScript (Variables and Functions)
title: "Hoisting in JavaScript (Variables and Functions)"
description: "Explore how hoisting works in JavaScript for variables and functions, including best practices to avoid common pitfalls."
publishDate: 2024-06-11
tags: ["javascript", "hoisting", "variables", "functions", "web-development"]
---

Hoisting is JavaScript’s default behavior of moving declarations to the top of their containing scope during the **creation phase**—before any code actually runs. However, only the declarations themselves are hoisted; initializations remain in place. Understanding hoisting thoroughly helps prevent confusing bugs and makes clear why certain patterns (and anti-patterns) behave as they do.

### 1. Function Declarations vs. Function Expressions

#### Function Declarations Are Fully Hoisted

Function declarations (the classic `function foo() { … }` form) are hoisted in their entirety. This means you can call them before they appear in your code:

```js
console.log(square(5)); // 25

function square(x) {
  return x * x;
}
```

- **Creation phase**: `square` → the entire function object.
    
- **Execution phase**: the call succeeds, since `square` was already defined.
    

#### Function Expressions Are Not Fully Hoisted

When you assign a function to a variable, only the variable declaration is hoisted—not the assignment:

```js
console.log(cube);       // undefined
// cube(3);              // TypeError: cube is not a function

var cube = function(x) {
  return x * x * x;
};

console.log(cube(3));    // 27
```

- **Creation**: `var cube` → initialized to `undefined`.
    
- **Execution**: the assignment `cube = function…` happens only when the engine reaches that line.
    

### 2. `var` Hoisting: Declaration, Not Initialization

Variables declared with `var` are hoisted and initialized to `undefined`. Accessing them before their declaration yields `undefined`, not a runtime error:

```js
console.log(a); // undefined
var a = 10;
console.log(a); // 10
```

Under the hood, this is equivalent to:

```js
var a;           // hoisted during creation
console.log(a);  // undefined
a = 10;          // actual assignment during execution
console.log(a);  // 10
```

#### Pitfall: Accidental Globals

If you assign to an undeclared identifier, JavaScript creates a global variable (in non-strict mode):

```js
function foo() {
  bar = 42;     // creates window.bar
}
foo();
console.log(bar); // 42
```

To avoid this, always use `var`, `let`, or `const`, and enable strict mode (`'use strict';`).

### 3. `let` & `const`: Hoisting with a Temporal Dead Zone (TDZ)

Unlike `var`, `let` and `const` are hoisted but remain **uninitialized** until their declaration is evaluated. Accessing them before that point throws a **ReferenceError** due to the **Temporal Dead Zone**:

```js
{
  // TDZ starts here
  // console.log(xLet);  // ReferenceError
  let xLet = 5;         // TDZ ends once declared
  console.log(xLet);    // 5
}

{
  // console.log(xConst); // ReferenceError
  const xConst = 10;    // must initialize at declaration
  console.log(xConst);  // 10
}
```

- **TDZ** spans from the beginning of the block until the declaration line.
    
- For `const`, you must both declare and initialize at the same time; reassignments are disallowed.
    

### 4. Detailed Example: Mixing `var`, `let`, and Functions

```js
console.log(foo);   // undefined (var hoisted)
console.log(bar);   // ReferenceError (in TDZ)
console.log(baz);   // [Function: baz]

var foo = 'hello';

let bar = 'world';

function baz() {
  return '!';
}
```

**Creation phase** builds:

- `foo` (var) → `undefined`
    
- `bar` (let) → _uninitialized_
    
- `baz` (function) → full function object
    

**Execution phase**:

1. `console.log(foo);` → `undefined`
    
2. `console.log(bar);` → ReferenceError (TDZ)
    
3. `console.log(baz);` → logs the function
    
4. `foo = 'hello';`
    
5. `bar = 'world';`
    

### 5. Hoisting and Block Scope

Variables declared with `var` ignore block boundaries, while `let`/`const` respect them:

```js
if (true) {
  var x = 1;
  let y = 2;
}
console.log(x); // 1
// console.log(y); // ReferenceError
```

Because `var x` is hoisted to the function/global scope, `x` remains accessible outside the `if` block. By contrast, `y` exists only within the block’s TDZ and scope.

### 6. Best Practices to Avoid Hoisting Pitfalls

1. **Declare at the Top of Scopes**  
    Placing `var` declarations at the top of functions (the style originally suggested by many style guides) makes hoisting explicit.
    
2. **Prefer `let` and `const`**  
    Their TDZ behavior prevents accidental use before initialization and clarifies scoping.
    
3. **Enable Strict Mode**  
    `'use strict';` turns accidental globals into errors, catching many hoisting-related bugs early.
    
4. **Avoid Relying on Hoisting**  
    Write code in the order you intend it to execute. Treat hoisting as an engine detail, not a feature to depend on.
    

---
