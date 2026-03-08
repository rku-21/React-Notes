# React Interview Guide - Concise Version

## 1. What is React, and what are its main features?

React is a JavaScript library developed by Facebook for creating user interfaces, particularly in single-page applications. It enables the use of reusable components that manage their own state. Key advantages include a component-driven architecture, optimized updates through the virtual DOM, a declarative approach for better readability, and robust community backing.

### Code Example:
```jsx
function Welcome() {
  const [name, setName] = React.useState('John');
  
  return (
    <div>
      <h1>Welcome, {name}!</h1>
      <button onClick={() => setName('Jane')}>Change</button>
    </div>
  );
}
```

---

## 2. What is JSX and how does it work?

JSX, short for JavaScript XML, is a syntax extension for JavaScript that allows you to write HTML-like code within JavaScript. It makes building React components easier. JSX gets converted into JavaScript function calls, often by Babel. For instance, `<div>Hello, world!</div>` is transformed into `React.createElement('div', null, 'Hello, world!')`.

### Code Example:
```jsx
// JSX syntax
const greeting = <h1>Hello!</h1>;

// Compiled to JavaScript
const greeting = React.createElement('h1', null, 'Hello!');

// JSX with expressions
function App({ name, age }) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Age: {age + 1}</p>
      {age > 18 ? <p>Adult</p> : <p>Minor</p>}
    </div>
  );
}

// JSX with lists
function List({ items }) {
  return (
    <ul>
      {items.map(item => <li key={item.id}>{item.name}</li>)}
    </ul>
  );
}
```

---

## 3. Explain the concept of the Virtual DOM in React.

The virtual DOM is a simplified version of the actual DOM used by React. It allows for efficient UI updates by comparing the virtual DOM to the real DOM and making only the necessary changes through a process known as reconciliation.

### Code Example:
```jsx
function Counter() {
  const [count, setCount] = React.useState(0);
  
  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <p>Static text</p>
    </div>
  );
}

// When count changes:
// React updates VDOM → compares with old VDOM → updates only <h1> in real DOM
// <button> and <p> remain unchanged (not re-rendered)
```

---

## 4. How does virtual DOM in React work? What are its benefits and downsides?

The virtual DOM in React is an in-memory representation of the real DOM. When state or props change, React creates a new virtual DOM tree, compares it to the previous one using a diffing algorithm, and efficiently updates only the parts of the real DOM that changed.

**Benefits:** 
- Improves performance by reducing costly direct DOM manipulations 
- Makes UI updates declarative and predictable
- Enables batch updates for better efficiency

**Downsides:** 
- Some overhead from diffing and extra memory usage
- In very dynamic UIs, it may not always outperform manual optimizations
- Additional abstraction layer can complicate debugging

### Code Example:
```jsx
// Batch updates - only ONE DOM update happens
function Form() {
  const [name, setName] = React.useState('');
  const [email, setEmail] = React.useState('');

  const handleSubmit = () => {
    setName('John');           // Batched
    setEmail('john@mail.com'); // Batched
    // React does 1 re-render, not 2
  };

  return <button onClick={handleSubmit}>Submit</button>;
}

// Performance: Only changed elements update
function DataDisplay({ title, count }) {
  return (
    <div>
      <h2>{title}</h2>
      <span>{count}</span>
    </div>
  );
}
// If only count changes, only <span> is updated
```

---

## 5. What is the difference Between React Node, React Element, and React Component?

A React Node refers to any unit that can be rendered in React, such as an element, string, number, or `null`. A React Element is an immutable object that defines what should be rendered, typically created using JSX or `React.createElement`. A React Component is either a function or class that returns React Elements, enabling the creation of reusable UI components.

### Code Example:
```jsx
// React Node - anything renderable
const stringNode = "Hello";
const numberNode = 42;
const elementNode = <div>Content</div>;
const arrayNode = [<p>A</p>, <p>B</p>];
const nullNode = null; // renders nothing

// React Element - immutable object
const element = <button>Click</button>;
// Actually: { type: 'button', props: { children: 'Click' } }

// React Component - function returning elements
function Button() {
  return <button>Click</button>;
}

// Different element objects with same content are NOT equal
const el1 = <h1>Title</h1>;
const el2 = <h1>Title</h1>;
console.log(el1 === el2); // false
```

---

## Common Interview Questions & Answers

### Q: What are Props?
Props are read-only data passed from parent to child components. They enable communication between components and make components reusable with different data.

```jsx
function UserCard({ name, email, age }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>{email}</p>
      <p>{age}</p>
    </div>
  );
}

<UserCard name="John" email="john@example.com" age={25} />
```

### Q: What is State?
State is mutable data managed within a component. When state changes, the component re-renders.

```jsx
function Counter() {
  const [count, setCount] = React.useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### Q: What are Hooks?
Hooks are functions that let you use state and other React features in functional components.

```jsx
// useState
const [count, setCount] = React.useState(0);

// useEffect
React.useEffect(() => {
  console.log('Component mounted');
}, []);

// useContext
const theme = React.useContext(ThemeContext);

// useReducer
const [state, dispatch] = React.useReducer(reducer, initialState);
```

### Q: Controlled vs Uncontrolled Components
Controlled components have state managed by React. Uncontrolled components manage their own state via the DOM.

```jsx
// Controlled - React manages state
function ControlledInput() {
  const [value, setValue] = React.useState('');
  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}

// Uncontrolled - DOM manages state
function UncontrolledInput() {
  const inputRef = React.useRef();
  return (
    <div>
      <input ref={inputRef} />
      <button onClick={() => alert(inputRef.current.value)}>Submit</button>
    </div>
  );
}
```

### Q: What is the Key prop in Lists?
The key prop helps React identify which items have changed. Use unique IDs, not indices.

```jsx
// ❌ Bad - using index
{items.map((item, index) => <li key={index}>{item}</li>)}

// ✅ Good - using unique ID
{items.map(item => <li key={item.id}>{item.name}</li>)}
```

### Q: What is Higher Order Component (HOC)?
A function that takes a component and returns an enhanced component with additional functionality.

```jsx
function withLogger(Component) {
  return (props) => {
    React.useEffect(() => {
      console.log('Component mounted');
    }, []);
    return <Component {...props} />;
  };
}

const LoggedComponent = withLogger(MyComponent);
```

### Q: What is Lifting State Up?
Moving shared state to the nearest common parent component so child components can share data.

```jsx
function Parent() {
  const [count, setCount] = React.useState(0);
  
  return (
    <div>
      <ChildA count={count} setCount={setCount} />
      <ChildB count={count} />
    </div>
  );
}

function ChildA({ count, setCount }) {
  return <button onClick={() => setCount(count + 1)}>Increment</button>;
}

function ChildB({ count }) {
  return <p>Count: {count}</p>;
}
```

---

## Quick Tips for Interview

✅ Know the basics: Components, Props, State, JSX
✅ Understand Virtual DOM: How it works and why it matters
✅ Know the differences: Node vs Element vs Component
✅ Practice writing code: Build components from scratch
✅ Understand Hooks: useState, useEffect, useContext
✅ Props vs State: Clear on when to use each
✅ Ask clarifications: If unsure, ask questions
✅ Explain your thinking: Walk through your logic
✅ Be honest: If you don't know something, say so
