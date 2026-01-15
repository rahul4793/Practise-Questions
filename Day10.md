## Q1. Difference Between var, let, and const in JavaScript

In JavaScript, var, let, and const are used to declare variables, but they differ significantly in scope, hoisting behavior, and reassignment rules. The var keyword has function scope, meaning it is accessible throughout the entire function in which it is declared, regardless of block boundaries. This can lead to unexpected behavior, especially in loops and conditional blocks. Additionally, var declarations are hoisted and initialized with undefined, which may cause runtime bugs if variables are used before assignment.

The let keyword was introduced in ES6 and provides block scope, meaning the variable is accessible only within the block where it is defined, such as inside an if statement or loop. This makes code more predictable and reduces accidental variable overwrites. let is hoisted but not initialized, which means accessing it before declaration results in a runtime error due to the temporal dead zone.

The const keyword is also block-scoped and is used to declare variables that should not be reassigned. Modern JavaScript prefers const by default because it communicates intent clearly and prevents accidental reassignment, leading to safer and more maintainable code. Developers typically use const unless reassignment is explicitly required, in which case let is used.

A const object can still be modified because const prevents reassignment of the variable reference, not mutation of the object itself. This means object properties can be changed, but the object cannot be reassigned to a new reference.
```
const user = { name: "Rahul" };
user.name = "Amit"; // allowed
// user = {}; // not allowed
```
## Q2. Closures in JavaScript

A closure in JavaScript is created when a function retains access to variables from its outer (lexical) scope even after the outer function has finished executing. Closures allow functions to “remember” the environment in which they were created. This behavior is a direct result of JavaScript’s lexical scoping model.

Closures are commonly used in real applications for data encapsulation, private state management, event handlers, and callback functions. For example, they are used in frameworks to maintain component state, in authentication flows to store tokens securely, and in utility functions that require persistent internal state.

A simple real-world example of a closure is a counter function that remembers its count value between calls. This allows controlled access to data without exposing it globally.
```
function createCounter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}

const counter = createCounter();
counter(); // 1
counter(); // 2

```
Closures help in data encapsulation by keeping variables private and inaccessible from the global scope. Only the inner function can access and modify those variables. However, closures can cause memory issues if not managed properly, especially when large objects are retained in memory unnecessarily due to lingering references. This can lead to memory leaks in long-running applications if closures are not released when no longer needed.

## Q3. Synchronous vs Asynchronous JavaScript

Synchronous JavaScript executes code line by line, blocking execution until the current operation completes. This means long-running operations such as network requests or file reads can freeze the application. Asynchronous JavaScript, on the other hand, allows time-consuming tasks to run in the background, enabling the application to remain responsive.

The JavaScript event loop is responsible for handling asynchronous behavior. It continuously checks the call stack and the task queues. When the call stack is empty, the event loop pushes pending callback functions from the queue into the stack for execution. This mechanism allows JavaScript to handle concurrency despite being single-threaded.

Callbacks were the earliest way to handle asynchronous operations but often led to deeply nested code known as callback hell. Promises improved this by providing a cleaner way to chain asynchronous operations and handle errors. Async/await, built on top of promises, allows asynchronous code to be written in a synchronous style, making it easier to read and maintain.

Async/await is preferred over callbacks because it improves readability, simplifies error handling using try-catch blocks, and reduces deeply nested code structures.

## Q4. Commonly Used ES6 Features in Projects

Modern JavaScript projects heavily rely on ES6 features to write cleaner and more expressive code. Arrow functions are widely used because they provide a concise syntax and do not bind their own this. Instead, they inherit this from the surrounding lexical scope, which eliminates common bugs related to context binding.

Destructuring allows developers to extract values from arrays or objects into variables in a clean and readable way. It is especially useful when working with API responses or function parameters.
```
const user = { name: "Rahul", age: 24 };
const { name, age } = user;

```
The spread operator is used to expand arrays or objects. It is commonly used for copying data immutably, merging objects, or passing arguments to functions.
```
const numbers = [1, 2, 3];
const newNumbers = [...numbers, 4];
```
## Q5. Difference Between == and === in JavaScript

The == operator compares two values after performing type coercion, meaning it converts the operands to the same type before comparison. This can lead to unexpected results and bugs. The === operator, also known as strict equality, compares both value and type without performing any type conversion.

Strict equality is preferred because it ensures predictable and consistent comparisons, reducing logical errors in applications. Type coercion occurs when JavaScript automatically converts one type to another, such as converting a string to a number during comparison.

"5" == 5;   // true
"5" === 5;  // false


A special case of type coercion is that null == undefined evaluates to true, while null === undefined evaluates to false. This is one of the reasons why === is recommended in modern JavaScript development.
