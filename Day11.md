## Q1. What is TypeScript and Why Is It Used Instead of Plain JavaScript

TypeScript is a superset of JavaScript developed by Microsoft that adds static typing and compile-time checks to JavaScript. It is used to catch errors early during development rather than at runtime, which is especially important in large and complex applications. TypeScript allows developers to write more predictable and self-documenting code while still using all existing JavaScript features.

TypeScript improves code maintainability by making data types explicit. When types are defined for variables, function parameters, and return values, developers can understand the intent of the code more easily. This reduces bugs, improves readability, and enables better tooling support such as intelligent code completion, refactoring, and navigation.

TypeScript is a compiled language, not interpreted. The TypeScript compiler (tsc) converts TypeScript code into plain JavaScript, which can then be executed by browsers or Node.js. Browsers cannot run TypeScript directly because they only understand JavaScript. The compilation step ensures compatibility across different environments.

## Q2. Types in TypeScript: any, unknown, and never

TypeScript provides a strong type system that helps enforce correctness at compile time. The any type disables type checking and allows a variable to hold any value. While it may be useful during rapid prototyping or migration from JavaScript, it should generally be avoided because it removes the benefits of TypeScript and can lead to runtime errors.

The unknown type is a safer alternative to any. It represents a value whose type is not known at compile time, but it forces the developer to perform type checks before using it. This ensures that unsafe operations are prevented unless the type is explicitly validated.

The never type represents values that never occur. It is commonly used as the return type of functions that either always throw an error or run indefinitely. It helps TypeScript ensure exhaustive checks, especially in switch statements.
```
function throwError(message: string): never {
  throw new Error(message);
}
```
## Q3. Interfaces in TypeScript and How They Differ from Classes

Interfaces in TypeScript define the shape of an object without providing implementation. They specify what properties and methods an object must have, but not how they work. Classes, on the other hand, define both structure and behavior and can be instantiated.

An interface can be extended to create a new interface with additional properties. This allows flexible and reusable design. A class can implement multiple interfaces, which promotes loose coupling and better separation of concerns.

Interfaces are especially useful in large applications because they act as contracts between different parts of the system. They help teams work independently, ensure consistency, and make refactoring safer by catching mismatches at compile time.
```
interface User {
  name: string;
  age: number;
}

class Employee implements User {
  name: string;
  age: number;
}
```
## Q4. Error Handling in JavaScript and TypeScript Applications

Error handling in JavaScript and TypeScript is primarily done using try–catch blocks, which allow developers to catch and handle runtime exceptions gracefully. This prevents applications from crashing and enables meaningful error messages or fallback behavior.

Promise rejections are handled using .catch() or try–catch when using async/await. Proper handling of promise rejections is critical to avoid unhandled promise errors, which can cause unexpected application behavior.

TypeScript helps reduce runtime errors by detecting type mismatches, missing properties, and incorrect function usage at compile time. While it does not eliminate all runtime errors, it significantly reduces their likelihood by enforcing stricter rules during development.

## Q5. Modules in JavaScript and TypeScript and Their Importance

Modules allow code to be organized into separate files with clear boundaries and responsibilities. They help prevent global namespace pollution and make applications easier to maintain, test, and scale. In TypeScript and modern JavaScript, modules are the foundation of large applications.

The import and export syntax is the modern standard for modules and is supported by both TypeScript and ES6 JavaScript. The older require syntax is part of CommonJS and is mainly used in older Node.js applications. Import and export are preferred because they support static analysis and tree-shaking.

A default export allows a module to export a single main value, which can be imported with any name. Named exports allow multiple exports from the same module. Modules help with dependency management by clearly defining what a file depends on and what it exposes, making the codebase easier to understand and evolve.
```
export default function log(message: string) {
  console.log(message);
}
```
