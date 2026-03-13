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


# React Interview Guide - Part 2

## 6. What are React Fragments used for?

React Fragments allow you to group multiple elements without adding extra nodes to the DOM. They are particularly useful when you need to return multiple elements from a component but don't want to wrap them in a container element. You can use shorthand syntax `<>...</>` or `React.Fragment`.

### Code Example:
```jsx
// Without Fragment - adds extra div to DOM
function Component() {
  return (
    <div>
      <ChildComponent1 />
      <ChildComponent2 />
    </div>
  );
}

// With Fragment - no extra node in DOM
function Component() {
  return (
    <>
      <ChildComponent1 />
      <ChildComponent2 />
    </>
  );
}

// Full Fragment syntax
return (
  <React.Fragment>
    <h1>Title</h1>
    <p>Content</p>
  </React.Fragment>
);
```

---

## 7. What is the purpose of the key prop in React?

In React, the key prop is used to uniquely identify elements in a list, allowing React to optimize rendering by updating and reordering items more efficiently. Without unique keys, React might re-render elements unnecessarily, causing performance problems and potential bugs.

### Code Example:
```jsx
// With keys - React knows which item is which
function TodoList({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.text}</li>
      ))}
    </ul>
  );
}

// Keys help with reordering
const items = [
  { id: 1, text: 'Learn React' },
  { id: 2, text: 'Build App' }
];
```

---

## 8. What is the consequence of using array indices as keys in React?

Using array indices as keys can lead to performance issues and unexpected behavior, especially when reordering or deleting items. React relies on keys to identify elements uniquely, and using indices can cause components to be re-rendered unnecessarily or display incorrect data.

### Code Example:
```jsx
// ❌ BAD - using index as key
function List({ items }) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{item.name}</li>
      ))}
    </ul>
  );
}
// Problem: If items are reordered or deleted, index changes and React gets confused

// ✅ GOOD - using unique ID as key
function List({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}
```

---

## 9. What are props in React? How are they different from state?

Props (short for properties) are inputs to React components that allow you to pass data from a parent component to a child component. They are immutable and are used to configure a component. State is internal to a component and can change over time, typically due to user interactions or other events.

### Code Example:
```jsx
// Props - passed from parent, immutable
function Child({ name, age }) {
  return <p>{name} is {age}</p>;
}

<Child name="John" age={25} />

// State - internal, mutable
function Counter() {
  const [count, setCount] = React.useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

| Props | State |
|-------|-------|
| Read-only | Mutable |
| Passed from parent | Internal to component |
| Cannot be modified by child | Can be modified |
| Used to configure component | Used for dynamic data |

---

## 10. What is the difference between React's class components and functional components?

Class components are ES6 classes that extend React.Component and can hold state, lifecycle methods, and other features. Functional components are simpler JavaScript functions that take props as input and return JSX. With hooks, functional components can now manage state and lifecycle methods.

### Code Example:
```jsx
// Class Component
class Welcome extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  
  render() {
    return (
      <div>
        <h1>Hello, {this.props.name}</h1>
        <p>Count: {this.state.count}</p>
      </div>
    );
  }
}

// Functional Component
function Welcome({ name }) {
  const [count, setCount] = React.useState(0);
  
  return (
    <div>
      <h1>Hello, {name}</h1>
      <p>Count: {count}</p>
    </div>
  );
}
```

---

## 11. When should you use a class component over a function component?

Class components are useful when you need to manage state, use lifecycle methods, or optimize performance through `shouldComponentUpdate`. However, with the introduction of hooks, functional components can now handle state and lifecycle methods, making them a preferred choice for most use cases due to their simplicity and readability.

### Code Example:
```jsx
// Functional Component with Hooks (Preferred)
function Component() {
  const [data, setData] = React.useState(null);
  
  React.useEffect(() => {
    fetchData().then(setData);
  }, []);
  
  return <div>{data}</div>;
}

// Class Component (Less common now)
class Component extends React.Component {
  componentDidMount() {
    fetchData().then(data => this.setState({ data }));
  }
  
  render() {
    return <div>{this.state.data}</div>;
  }
}
```

---

## 12. What is React Fiber?

React Fiber is a complete rewrite of the React core algorithm, designed to improve performance and enable new features like async rendering, error boundaries, and incremental rendering. It breaks down the rendering process into smaller chunks, allowing React to pause, abort, or prioritize updates as needed.

### Key Points:
- Improves performance with incremental rendering
- Enables priority-based updates
- Allows React to pause and resume work
- Better error handling with error boundaries

```jsx
// Error Boundary Example (uses Fiber)
class ErrorBoundary extends React.Component {
  componentDidCatch(error, errorInfo) {
    console.log(error);
  }
  
  render() {
    return this.props.children;
  }
}
```

---

## 13. What is reconciliation?

Reconciliation is the process by which React updates the DOM to match the virtual DOM efficiently. It involves comparing the new virtual DOM tree with the previous one and determining the minimum number of changes required to update the actual DOM. This process ensures optimal performance by avoiding unnecessary re-renders.

### Code Example:
```jsx
function Component({ value }) {
  return (
    <div>
      <h1>{value}</h1>
      <p>Static text</p>
    </div>
  );
}

// When value changes:
// React compares old VDOM with new VDOM
// Finds only <h1> changed
// Updates only <h1> in real DOM, <p> stays same
```

---

## 14. What is the difference between Shadow DOM and Virtual DOM?

**Shadow DOM** is a web standard that encapsulates a part of the DOM, isolating it from the rest of the document. It's used for creating reusable, self-contained components without affecting global styles or scripts.

**Virtual DOM** is an in-memory representation of the actual DOM used by React to optimize rendering. It compares current and previous states, updating only necessary parts of the DOM.

### Key Differences:
```
Shadow DOM:
- Browser standard
- Encapsulates DOM and styles
- Used by web components
- Real DOM that's hidden

Virtual DOM:
- React concept
- In-memory representation
- Optimizes rendering
- Not a real DOM
```

---

## 15. What is the difference between Controlled and Uncontrolled React components?

In controlled components, form data is managed through the component's state. In uncontrolled components, form state is managed internally and accessed via refs.

### Code Example:
```jsx
// Controlled Component - React manages state
function ControlledInput() {
  const [value, setValue] = React.useState('');
  
  return (
    <input
      type="text"
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
}

// Uncontrolled Component - DOM manages state
function UncontrolledInput() {
  const inputRef = React.useRef();
  
  return (
    <div>
      <input type="text" ref={inputRef} />
      <button onClick={() => alert(inputRef.current.value)}>Submit</button>
    </div>
  );
}
```

| Controlled | Uncontrolled |
|-----------|--------------|
| State managed by React | State in DOM |
| React is source of truth | DOM is source of truth |
| More control | Simpler code |
| Easier to test | Less boilerplate |

---

## 16. How would you lift the state up in a React application, and why is it necessary?

Lifting state up in React involves moving state from child components to their nearest common ancestor. This pattern is used to share state between components that don't have a direct parent-child relationship. By lifting state up, you avoid prop drilling and simplify shared data management.

### Code Example:
```jsx
// State lifted to Parent
function Parent() {
  const [count, setCount] = React.useState(0);
  
  return (
    <div>
      <Child1 count={count} />
      <Child2 setCount={setCount} />
    </div>
  );
}

function Child1({ count }) {
  return <h1>Count: {count}</h1>;
}

function Child2({ setCount }) {
  return (
    <button onClick={() => setCount(prev => prev + 1)}>
      Increment
    </button>
  );
}
```

---

## 17. What are Pure Components?

Pure Components in React are components that only re-render when their props or state change. They use shallow comparison to check if props or state have changed, preventing unnecessary re-renders and improving performance.

### Code Example:
```jsx
// Class Pure Component
class PureCounter extends React.PureComponent {
  render() {
    return <div>{this.props.count}</div>;
  }
}

// Functional Pure Component with React.memo
const PureCounter = React.memo(function ({ count }) {
  return <div>{count}</div>;
});

// With custom comparison
const PureCounter = React.memo(
  ({ count }) => <div>{count}</div>,
  (prevProps, nextProps) => prevProps.count === nextProps.count
);
```

---

## 18. What is the difference between createElement and cloneElement?

**createElement** creates a new React element from scratch with the specified type, props, and children.

**cloneElement** clones an existing React element and allows you to modify its props while keeping the original element's children and structure.

### Code Example:
```jsx
// createElement - creates new element
const element = React.createElement('div', { className: 'box' }, 'Hello');

// cloneElement - clones and modifies existing element
const originalElement = <button className="btn">Click</button>;
const clonedElement = React.cloneElement(originalElement, { className: 'btn-primary' });

// Practical example
function Wrapper(props) {
  return React.cloneElement(props.children, { 
    className: 'wrapped ' + props.children.props.className 
  });
}
```

---

## 19. What is the role of PropTypes in React?

PropTypes in React is used for type-checking props passed to components, ensuring the correct data types are used and warning developers during development if wrong types are passed.

### Code Example:
```jsx
import PropTypes from 'prop-types';

function MyComponent({ name, age, email }) {
  return <div>{name} is {age}</div>;
}

MyComponent.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number.isRequired,
  email: PropTypes.string,
  items: PropTypes.array,
  isActive: PropTypes.bool
};

// Default props
MyComponent.defaultProps = {
  email: 'unknown@example.com'
};
```

---

## 20. What are stateless components?

Stateless components in React are components that do not manage or hold any internal state. They simply receive data via props and render UI based on that data. These components are often functional components used for presentational purposes.

### Key Points:
- Do not use `this.state` or `useState`
- Render UI based on props
- Focused on displaying information, not managing behavior
- Simpler and easier to test

### Code Example:
```jsx
// Stateless Component
function Greeting({ name, age }) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Age: {age}</p>
    </div>
  );
}

// Just presents data from props
function Card({ title, description }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <p>{description}</p>
    </div>
  );
}
```

---

## 21. What are stateful components?

Stateful components in React are components that manage and hold their own internal state. They can modify their state in response to user interactions or other events and re-render themselves when the state changes.

### Key Points:
- Use `this.state` (class) or `useState` (functional)
- Can update state using event handlers or lifecycle methods
- Handle logic and data management
- Essential for dynamic and interactive UIs

### Code Example:
```jsx
// Stateful Component with Hooks
function Counter() {
  const [count, setCount] = React.useState(0);
  const [name, setName] = React.useState('');
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <p>Name: {name}</p>
    </div>
  );
}

// Class Component Version
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  
  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}
```

---





