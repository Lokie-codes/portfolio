---
# Top 9 JavaScript Topics to Know Before React JS
title: "Top 9 JavaScript Topics to Know Before React JS"
description: "A comprehensive guide to the essential JavaScript concepts you should master before diving into React JS, including let/const, arrow functions, destructuring, and more."
publishDate: 2023-07-18
tags: ["javascript", "react", "best-practices", "web-development"]
---
Before diving into React’s component model and hooks, a solid grounding in core JavaScript syntax and semantics will make your learning far smoother. In this episode, we take each of the nine recommended topics and explore why it matters for React, complete with in-depth examples and best practices.

## 1. `let` / `const` & Block Scoping

### Why It Matters in React

React components often maintain local state and temporary variables. Mis-scoped variables can lead to bugs that only surface under specific renders or event sequences.

### Deep Details

- **`var` vs. `let`/`const`**
    
    - `var` is function-scoped; it “hoists” to the top of the nearest function, leading to surprises in loops and conditionals.
        
    - `let` and `const` are block-scoped—visible only inside the nearest `{ … }`.
        
- **Temporal Dead Zone (TDZ)**  
    Any `let`/`const` binding is “uninitialized” until its declaration line. Accessing it earlier throws a ReferenceError:
    
    ```js
    {
      // TDZ starts here for `x`
      // console.log(x); // ReferenceError
      let x = 10;      // TDZ ends
      console.log(x);  // 10
    }
    ```
    

### Examples

#### Loop Counters in Event Handlers

```jsx
// ❌ using var
for (var i = 0; i < buttons.length; i++) {
  buttons[i].addEventListener('click', () => {
    console.log('Clicked button', i);
  });
}
// Always logs the last index

// ✅ with let
for (let i = 0; i < buttons.length; i++) {
  buttons[i].addEventListener('click', () => {
    console.log('Clicked button', i);
  });
}
// Logs each correct i: 0,1,2...
```

#### Immutable vs. Mutable Bindings

```js
const arr = [1,2,3];
arr.push(4);        // OK: mutating contents
// arr = [5,6];    // TypeError: cannot reassign const

let counter = 0;
counter += 1;       // OK: let is mutable
```

### Pitfalls & Tips

- **Never mix** `var` with `let`/`const`—it undermines block scope.
    
- **Default to `const`**: only switch to `let` when you know you’ll reassign.
    
- **Declare as late as possible**: minimizes TDZ window and clarifies usage.
    

---

## 2. Arrow Functions & Lexical `this`

### Why It Matters in React

Event handlers, hooks callbacks, and functional components rely on correct `this` or capturing outer context.

### Deep Details

- **No own `this`**: arrow functions inherit `this` from the defining scope.
    
- **No `arguments` object**: use rest parameters instead.
    
- **Cannot be used as constructors**: they lack an internal `[[Construct]]` method.
    

### Examples

#### Binding in Class Components

```jsx
class MyButton extends React.Component {
  // Arrow method → `this` always the class instance
  handleClick = () => {
    console.log(this.props.label);
  }

  render() {
    return <button onClick={this.handleClick}>{this.props.label}</button>;
  }
}
```

Without the arrow, you’d need:

```js
constructor(props) {
  super(props);
  this.handleClick = this.handleClick.bind(this);
}
```

#### Losing vs. Keeping Context

```js
const obj = {
  id: 1,
  regular() { console.log(this.id); },
  arrow:  () => console.log(this.id),
};

obj.regular(); // 1
obj.arrow();   // undefined (lexical this → outer/global scope)
```

### Advanced Tip: Methods vs. Properties

Defining arrow methods as class properties moves them onto each instance, not the prototype—slightly higher memory use but simpler binding.

---

## 3. Destructuring & Spread/Rest

### Why It Matters in React

Props objects, state updates, and styled-component overrides all benefit from concise extraction and merging.

### Deep Details

- **Object Destructuring**
    
    ```js
    const { name, age = 18, ...others } = props;
    ```
    
    - Default values (`= 18`) when undefined
        
    - Rest collects remaining keys
        
- **Array Destructuring**
    
    ```js
    const [first, , third = null] = items;
    ```
    
- **Spread in Objects** (ES2018)
    
    ```js
    const newState = { ...this.state, count: this.state.count + 1 };
    ```
    

### Examples

#### Props with Defaults

```jsx
function Card({ title, children, highlight = false }) {
  return (
    <div className={`card ${highlight ? 'card--highlight' : ''}`}>
      <h2>{title}</h2>
      {children}
    </div>
  );
}
```

#### Merging Configuration

```js
const defaultConfig = { debug: false, endpoint: '/api' };
const userConfig    = { debug: true };
const finalConfig   = { ...defaultConfig, ...userConfig };
// { debug: true, endpoint: '/api' }
```

### Pitfalls & Tips

- **Order matters**: later spreads overwrite earlier properties.
    
- **Deep cloning**: spread only shallow-copies—nested objects still share references. Use utilities (e.g., `lodash.cloneDeep`) for deep copies.
    

---

## 4. Template Literals

### Why It Matters in React

Building dynamic class names, inline styles, or large chunks of markup is far clearer with backticks.

### Deep Details

- **Interpolation**: `${expression}`
    
- **Multi-line strings**: preserve line breaks
    
- **Tagged templates**: custom processing
    

### Examples

#### Dynamic Class Names

```jsx
function Button({ type, disabled }) {
  return (
    <button
      className={`btn btn--${type} ${disabled ? 'btn--disabled' : ''}`}
      disabled={disabled}>
      Click me
    </button>
  );
}
```

#### HTML or SQL via Tagged Templates

```js
function htmlEscape(strings, ...values) {
  // escape `values` and recombine
}
const safe = htmlEscape`<div>${userInput}</div>`;
```

### Pitfalls & Tips

- **Injection risks**: when interpolating user data into HTML/SQL, sanitize first.
    
- **Line breaks**: template literals include them—be mindful when embedding in JSX.
    

---

## 5. Modules (ESM)

### Why It Matters in React

Modern build systems (Webpack/Vite) leverage ESM for code-splitting and tree-shaking.

### Deep Details

- **Named vs. Default Exports**
    
    ```js
    export const PI = 3.14;
    export function sum(a, b) { return a + b; }
    export default function main() { … }
    ```
    
- **Dynamic Imports**
    
    ```js
    import('lodash').then(_ => console.log(_.cloneDeep(obj)));
    ```
    
- **Tree-Shaking**  
    Only imported bindings are bundled, reducing code size.
    

### Examples

```js
// utils/math.js
export function add(a, b) { return a + b; }

// index.js
import { add } from './utils/math.js';
console.log(add(2,3)); // 5

// Lazy load a heavy component
const Heavy = React.lazy(() => import('./components/Heavy'));
```

### Pitfalls & Tips

- **Circular dependencies** can result in undefined imports at runtime.
    
- **File extensions**: in some environments you must include `.js` in import paths.
    

---

## 6. Promises & `async`/`await`

### Why It Matters in React

Data fetching, side effects in hooks, and event-driven flows become far easier to reason about.

### Deep Details

- **Promise chaining** vs. **async/await** (see Episode 2–4 expansions).
    
- **Cancellation**: built-in Promises can’t cancel—use `AbortController` for fetch or external flags.
    

### Examples

#### Fetch in `useEffect`

```jsx
function Data({ url }) {
  const [data, setData] = useState(null);
  useEffect(() => {
    const ctrl = new AbortController();
    async function load() {
      try {
        const res = await fetch(url, { signal: ctrl.signal });
        const json = await res.json();
        setData(json);
      } catch (err) {
        if (err.name !== 'AbortError') console.error(err);
      }
    }
    load();
    return () => ctrl.abort();
  }, [url]);
  return data ? <Display data={data}/> : <Loading />;
}
```

### Pitfalls & Tips

- **Stale closures**: always include dependencies in `useEffect`’s array.
    
- **Unhandled rejections**: attach `.catch` or wrap in `try/catch`.
    

---

## 7. Classes & Prototypes

### Why It Matters in React

Understanding how `extends React.Component` works and why methods live on prototypes.

### Deep Details

- **Constructor functions** vs. **class syntax**
    
- **Prototype inheritance**: methods on `MyClass.prototype` shared across instances.
    
- **`super`**: how subclass constructors link into the prototype chain.
    

### Examples

```js
class Animal {
  constructor(name) { this.name = name; }
  speak() { console.log(`${this.name} makes a noise.`); }
}

class Dog extends Animal {
  speak() {
    super.speak();
    console.log(`${this.name} barks.`);
  }
}

const d = new Dog('Rex');
d.speak();
// Rex makes a noise.
// Rex barks.
```

### Pitfalls & Tips

- **Binding methods**: class methods aren’t auto-bound; use arrow properties or bind in the constructor.
    
- **Mixing prototypes and instance properties** can confuse memory usage.
    

---

## 8. Event Loop & Async Patterns

### Why It Matters in React

Knowing when state updates flush, how microtasks vs. macrotasks interleave, and why some effects run after paint.

### Deep Details

- **Call Stack → Web APIs → Task Queues → Event Loop**
    
- **Microtasks** (Promises) vs. **Macrotasks** (setTimeout, I/O)
    
- **React’s batching** in event handlers vs. timeouts
    

### Examples

```js
console.log('A');
setTimeout(() => console.log('B'), 0);
Promise.resolve().then(() => console.log('C'));
console.log('D');
// Output: A, D, C, B
```

### Pitfalls & Tips

- **State updates** inside async callbacks may batch differently—use the functional `setState(prev => next)` form to avoid stale values.
    
- **Long tasks** block rendering; break them with `setTimeout(..., 0)` or `requestIdleCallback`.
    

---

## 9. DOM Manipulation Basics

### Why It Matters in React

When you need to integrate libraries or handle off-React UI elements.

### Deep Details

- **Refs** (`useRef` / `createRef`) for direct node access
    
- **Event delegation** vs. individual listeners
    
- **Standard APIs**: `querySelector`, `addEventListener`, `classList`, DOM attributes
    

### Examples

#### Integrating a jQuery Plugin

```jsx
function DatePicker({ options }) {
  const ref = useRef(null);

  useEffect(() => {
    $(ref.current).datepicker(options);
    return () => $(ref.current).datepicker('destroy');
  }, [options]);

  return <input ref={ref} />;
}
```

#### Event Delegation

```js
document.getElementById('list').addEventListener('click', e => {
  if (e.target.matches('li')) {
    console.log('Clicked list item:', e.target.textContent);
  }
});
```

### Pitfalls & Tips

- **Avoid manual DOM changes** that conflict with React’s virtual DOM—keep them isolated.
    
- **Clean up** any listeners or third-party instances in `useEffect`’s cleanup.
    

---

