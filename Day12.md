## 1. UserProfile Component Using Props

A UserProfile component can be created as a reusable presentational component that receives data through props. Props allow parent components to pass data downward, making components reusable and predictable. In this case, name, email, and city are passed as props and displayed inside a simple card layout.
```
function UserProfile({ name, email, city }) {
  return (
    <div className="card">
      <h3>{name}</h3>
      <p>{email}</p>
      <p>{city}</p>
    </div>
  );
}
```

Props are read-only, meaning a child component cannot modify them. This ensures a unidirectional data flow, which makes React applications easier to debug and reason about. State, on the other hand, is mutable and managed within the component itself. Props represent external data, while state represents internal, changeable data.

## 2. Counter Component Using State

A counter component uses React state to track changing values such as count. State updates trigger re-rendering, allowing the UI to stay in sync with data changes.
```
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </>
  );
}
```

State updates should always be immutable because React relies on reference changes to detect updates efficiently. If state is updated directly, React may not detect the change, leading to stale UI or unpredictable behavior. Direct mutation can also break React’s batching and optimization mechanisms.

## 3. useEffect for API Fetch and Cleanup

The useEffect hook is used to perform side effects such as data fetching or timers. When fetching data on component mount, an empty dependency array is used so the effect runs only once.
```
import { useEffect, useState } from "react";

function DataFetcher() {
  const [data, setData] = useState([]);

  useEffect(() => {
    fetch("https://api.example.com/data")
      .then(res => res.json())
      .then(setData);

    const timer = setInterval(() => {
      console.log("Timer running");
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  return <div>{data.length}</div>;
}
```

The dependency array controls when the effect runs. Omitting it causes the effect to run on every render, often leading to infinite loops. Infinite loops occur when state updated inside useEffect is also listed as a dependency without proper conditions.

## 4. Rendering a List Using map()

Rendering lists in React is done using map(). Each element must have a unique key so React can efficiently update only the changed elements.
```
function ProductList({ products }) {
  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```

Keys are important because they help React identify which items have changed, been added, or removed. Using array index as a key is discouraged because it can cause incorrect UI updates when items are reordered, inserted, or deleted.

## 5. SearchBox and ProductList with Lifted State

When two components need to share state, the state is lifted up to their closest common parent. In this case, the search query is maintained in the parent and passed down to both components.
```
function ProductsContainer() {
  const [query, setQuery] = useState("");
  const products = ["Apple", "Banana", "Orange"];

  const filtered = products.filter(p =>
    p.toLowerCase().includes(query.toLowerCase())
  );

  return (
    <>
      <SearchBox onSearch={setQuery} />
      <ProductList products={filtered} />
    </>
  );
}
```

Lifting state up centralizes shared data and keeps components in sync. Alternatives include Context API, Redux, Zustand, or passing callbacks through custom hooks when prop drilling becomes excessive.

## 6. useMemo and useCallback

useMemo is used to memoize values, while useCallback is used to memoize functions. They help avoid unnecessary recalculations and re-renders.
```
const expensiveValue = useMemo(() => {
  return heavyComputation(data);
}, [data]);

const handleClick = useCallback(() => {
  setCount(c => c + 1);
}, []);
```

The main difference is that useMemo returns a computed value, while useCallback returns a memoized function. They should not be overused because they add complexity and memory overhead. They are most useful when dealing with expensive computations or preventing unnecessary child re-renders.

## 7. Theme Toggle Using Context API

The Context API allows global state sharing without prop drilling. A theme toggle is a common example.
```
const ThemeContext = createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

```
Context API is ideal for global UI state such as themes or language settings. However, it is not recommended for high-frequency updates or complex business logic. Redux is preferred when state management becomes large, predictable, and needs middleware or debugging tools.

## 8. Controlled Login Form with Validation

In a controlled component, form values are managed by React state. This allows real-time validation and full control over user input.
```
function LoginForm() {
  const [email, setEmail] = useState("");
  const [error, setError] = useState("");

  const handleSubmit = e => {
    e.preventDefault();
    if (!email) {
      setError("Email is required");
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={e => setEmail(e.target.value)} />
      {error && <p>{error}</p>}
      <button type="submit">Login</button>
    </form>
  );
}
```

Controlled components give React full control over form state, while uncontrolled components rely on the DOM using refs. Compared to Angular, React form validation is more manual and flexible, whereas Angular provides built-in form validation frameworks.
