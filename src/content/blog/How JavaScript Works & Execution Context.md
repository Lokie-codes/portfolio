---
# How JavaScript Works: Execution Context and the Call Stack
title: "How JavaScript Works: Execution Context and the Call Stack"
description: "A deep dive into how JavaScript manages execution contexts, the call stack, and the lifecycle of code execution, including variable hoisting and the `this` keyword."
publishDate: 2024-01-15
tags: ["javascript", "execution-context", "call-stack", "hoisting", "this"]
---
JavaScript code doesn’t simply “run” top to bottom—under the hood, the engine orchestrates two distinct phases for every piece of code: the **creation (or memory-allocation) phase** and the **execution phase**. Understanding these phases is crucial for grasping how variables, functions, and the special `this` reference are managed at runtime.

### 1. Creation Phase: Setting Up the Environment

When the JavaScript engine first encounters a script or function, it enters the **creation phase**, during which it:

1. **Allocates Memory**
    
    - Every declared variable and function is registered in a special storage called the **Variable Object** (VO) for that context.
        
    - For the **global context**, this VO is often referred to as the **global object** (in browsers, `window`; in Node, `global`).
        
2. **Registers Function Declarations**
    
    - Entire function bodies are stored, making them callable anywhere in their scope—even before their textual appearance (this is why function declarations are “hoisted”).
        
3. **Registers Variable Declarations**
    
    - Variables declared with `var` are created and initialized to `undefined`.
        
    - Variables declared with `let` and `const` are also noted, but remain uninitialized until their actual declaration line—any access before that line triggers a **ReferenceError** due to the **Temporal Dead Zone**.
        
4. **Establishes the Scope Chain**
    
    - Each context keeps a reference to its **outer environment**, forming a chain that the engine will later traverse when resolving identifiers.
        
    - For the global context, this chain ends at the “null” top.
        
5. **Determines the Value of `this`**
    
    - In the global context, `this` points to the global object (e.g., `window`).
        
    - In function contexts (non-strict mode), calls without a receiver also default `this` to the global object; in strict mode, `this` remains `undefined` unless explicitly set.
        

At the end of creation, every name (“foo”, “bar”, etc.) knows its place in memory, but none of the code statements have actually executed yet.

### 2. Execution Phase: Running Your Code

With the groundwork laid, the engine switches to the **execution phase**, processing statements line by line:

- **Variable Initializations** occur when their declaration is reached (e.g., `a = 5` assigns `5` to `a`).
    
- **Function Calls** trigger new, nested execution contexts.
    
- **Expressions** are evaluated, and control structures (`if`, `for`, etc.) drive the flow.
    

Crucially, the engine maintains a **Call Stack** to track which context is currently active:

1. **Push Global Execution Context**  
    As soon as your script starts, its global context is pushed onto the stack.
    
2. **Function Invocation → Push New Context**  
    When you invoke `foo()`, the engine creates a **Function Execution Context** (FEC), runs its creation phase (hoisting, VO setup), then executes its body.
    
3. **Function Returns → Pop Context**  
    After `foo` completes and returns a value (or implicitly `undefined`), its context is popped off the stack, and execution resumes where it left off.
    

This stack discipline ensures isolation between calls: local variables in one function can’t accidentally overwrite those in another.

### 3. Anatomy of an Execution Context

Every execution context—global or function—internally comprises:

- **Variable Object (VO)**  
    Holds function parameters, local variables (`var`), and function declarations. In spec terminology, this is now represented by the **Environment Record**.
    
- **Scope Chain**  
    An ordered list of pointers: current VO → outer VO → … → global VO. When you reference `x`, the engine walks this chain until it finds an `x` or throws a ReferenceError.
    
- **`this` Binding**  
    The value of `this` is determined by how the function is called:
    
    - As a method: `obj.method()`, `this` → `obj`
        
    - Standalone (non-strict): `foo()`, `this` → global object
        
    - Standalone (strict): `foo()`, `this` → `undefined`
        
    - With `call`/`apply`/`bind`: explicitly set
        

### 4. Putting It All Together: An Example

```js
// Global creation: x → undefined, foo → [function]
// this (global) → window

let x = 10;               // execution: assign 10 to x

function foo(y) {         // foo already in VO from creation
  let z = x + y;          // execution: x looked up via scope chain
  return z;
}

console.log(foo(5));      // call stack: [Global] → push foo’s context
                          // foo’s creation: y → undefined, z → undefined
                          // execution: initialize y=5; compute z=15; return 15
                          // pop foo’s context → back to Global
                          // console.log prints 15
```

1. **Creation Phase (Global)** sets up `x` and `foo`.
    
2. **Execution Phase (Global)** assigns `10` to `x`, then defines `foo`.
    
3. When calling `foo(5)`, a **Function EC** is created for `foo`: its own VO (with `y` and `z`), the same scope chain linking back to global, and `this` (undefined in strict mode, or global in non-strict).
    
4. Inside `foo`, `x` is resolved upward via the scope chain, demonstrating how outer variables remain accessible.
    
5. After `foo` returns, its context is disposed, freeing `y` and `z`.
    

---
