# 06. JavaScript Functions

Functions are one of the fundamental building blocks in JavaScript. A function is a reusable block of code designed to perform a particular task. Functions allow you to organize your code into manageable pieces, promote reusability, and make your programs more modular.

## Table of Contents
1.  [What is a Function?](#what-is-function)
2.  [Defining Functions](#defining-functions)
    *   [Function Declaration (Statement)](#function-declaration)
    *   [Function Expression](#function-expression)
    *   [Anonymous Functions](#anonymous-functions)
    *   [Named Function Expressions](#named-function-expressions)
3.  [Arrow Functions (ES6)](#arrow-functions)
4.  [Invoking (Calling) Functions](#invoking-functions)
5.  [Parameters and Arguments](#parameters-arguments)
    *   [Default Parameters (ES6)](#default-parameters)
    *   [Rest Parameters (ES6)](#rest-parameters)
    *   [The `arguments` Object (Pre-ES6)](#arguments-object)
6.  [The `return` Statement](#return-statement)
7.  [Function Scope](#function-scope)
    *   [Global Scope](#global-scope)
    *   [Local (Function) Scope](#local-scope)
    *   [Block Scope (with `let` and `const`)](#block-scope-functions)
8.  [Hoisting](#hoisting-functions)
    *   [Function Declarations Hoisting](#declaration-hoisting)
    *   [Function Expressions Hoisting](#expression-hoisting)
9.  [Immediately Invoked Function Expressions (IIFE)](#iife)
10. [Functions as First-Class Citizens](#first-class-functions)
11. [Callbacks (Brief Introduction)](#callbacks-intro)
12. [Best Practices for Functions](#best-practices-functions)
13. [Key Takeaways](#key-takeaways-functions)

## 1. What is a Function? <a name="what-is-function"></a>
A function is a set of statements that performs a task or calculates a value. To use a function, you must define it somewhere in the scope from which you wish to call it.

Key characteristics:
*   **Reusability**: Write code once and use it multiple times.
*   **Modularity**: Break down complex problems into smaller, manageable pieces.
*   **Abstraction**: Hide complex implementation details and expose a simple interface.

## 2. Defining Functions <a name="defining-functions"></a>
There are several ways to define functions in JavaScript.

### Function Declaration (Statement) <a name="function-declaration"></a>
This is the most common way to define a function. It starts with the `function` keyword, followed by the function name, a list of parameters in parentheses, and a block of code (the function body) enclosed in curly braces `{}`.

**Syntax:**
```javascript
function functionName(parameter1, parameter2) {
  // Code to be executed
  // ...
  return value; // Optional
}
```

**Example:**
```javascript
function greet(name) {
  console.log(`Hello, ${name}!`);
}

function add(a, b) {
  return a + b;
}
```
Function declarations are hoisted (see [Hoisting](#hoisting-functions) section).

### Function Expression <a name="function-expression"></a>
A function can also be defined as part of an expression, typically by assigning it to a variable. The function itself can be named or anonymous.

**Syntax (Anonymous):**
```javascript
const variableName = function(parameter1, parameter2) {
  // Code to be executed
};
```

**Example (Anonymous):**
```javascript
const sayGoodbye = function(name) {
  console.log(`Goodbye, ${name}!`);
};

const multiply = function(x, y) {
  return x * y;
};
```
Function expressions are *not* fully hoisted in the same way as declarations; the variable declaration is hoisted, but the function assignment is not.

### Anonymous Functions <a name="anonymous-functions"></a>
An anonymous function is a function without a name. They are often used in function expressions or as arguments to other functions (callbacks).
```javascript
const numbers = [1, 2, 3];
numbers.map(function(num) { // Anonymous function used as a callback
  return num * 2;
});
```

### Named Function Expressions <a name="named-function-expressions"></a>
A function expression can also have a name. This name is primarily useful for debugging (it appears in stack traces) and for recursion within the function itself.
```javascript
const factorial = function fac(n) {
  if (n <= 1) {
    return 1;
  }
  return n * fac(n - 1); // Can call 'fac' recursively
};

console.log(factorial(5)); // 120
// console.log(fac(5)); // Error: fac is not defined in the outer scope
```
The name of a named function expression is only available within the function's own scope.

## 3. Arrow Functions (ES6) <a name="arrow-functions"></a>
Arrow functions, introduced in ES6, provide a more concise syntax for writing function expressions. They also have some differences in behavior regarding `this`, `arguments`, and `prototype`.

**Syntax:**
```javascript
// Single parameter, single expression (implicit return)
const functionName = parameter => expression;

// Multiple parameters, single expression (implicit return)
const functionName = (param1, param2) => expression;

// Single parameter, block body (explicit return needed)
const functionName = parameter => {
  // statements
  return value;
};

// Multiple parameters, block body (explicit return needed)
const functionName = (param1, param2) => {
  // statements
  return value;
};

// No parameters
const functionName = () => expression;
const functionName = () => {
  // statements
  return value;
};
```

**Examples:**
```javascript
const greetArrow = name => `Hello, ${name}!`;
console.log(greetArrow("Arrow")); // Hello, Arrow!

const sumArrow = (a, b) => a + b;
console.log(sumArrow(5, 10)); // 15

const processData = data => {
  console.log("Processing...");
  return data.toUpperCase();
};
console.log(processData("test")); // TEST
```
Key characteristics of arrow functions:
*   **Concise syntax**.
*   **Lexical `this` binding**: Arrow functions do not have their own `this` context. They inherit `this` from the surrounding (enclosing) lexical scope. This is often very helpful in callbacks and methods.
*   **No `arguments` object**: Use rest parameters (`...args`) instead.
*   Cannot be used as constructors (cannot be called with `new`).
*   Do not have a `prototype` property.

## 4. Invoking (Calling) Functions <a name="invoking-functions"></a>
Defining a function does not execute it. To execute a function, you must *invoke* or *call* it by using its name followed by parentheses `()`.

```javascript
function sayHello() {
  console.log("Hello there!");
}
sayHello(); // Invokes the function, output: Hello there!

const result = add(10, 5); // Invokes 'add' and stores the returned value
console.log(result); // 15
```

## 5. Parameters and Arguments <a name="parameters-arguments"></a>
*   **Parameters**: Variables listed in the function definition (e.g., `name` in `function greet(name)`).
*   **Arguments**: The actual values passed to the function when it is invoked (e.g., `"Alice"` in `greet("Alice")`).

```javascript
function introduce(name, age) { // 'name' and 'age' are parameters
  console.log(`My name is ${name} and I am ${age} years old.`);
}

introduce("Bob", 30); // "Bob" and 30 are arguments
```
If a function is called with fewer arguments than declared parameters, the missing parameters will have the value `undefined`.
If called with more arguments, the extra arguments are accessible via the `arguments` object (for traditional functions) or can be ignored.

### Default Parameters (ES6) <a name="default-parameters"></a>
You can provide default values for parameters if no argument is passed or if `undefined` is passed.

```javascript
function greetUser(name = "Guest") {
  console.log(`Hello, ${name}!`);
}

greetUser("Alice"); // Hello, Alice!
greetUser();        // Hello, Guest!
```

### Rest Parameters (ES6) <a name="rest-parameters"></a>
The rest parameter syntax allows a function to accept an indefinite number of arguments as an array.

```javascript
function sumAll(...numbers) { // 'numbers' will be an array
  let total = 0;
  for (let num of numbers) {
    total += num;
  }
  return total;
}

console.log(sumAll(1, 2, 3));       // 6
console.log(sumAll(10, 20, 30, 40)); // 100
console.log(sumAll());              // 0
```
A function can have only one rest parameter, and it must be the last parameter.

### The `arguments` Object (Pre-ES6) <a name="arguments-object"></a>
Inside any non-arrow function, an `arguments` object is available. It's an array-like object (not a true array) containing all arguments passed to the function, regardless of the named parameters.

```javascript
function logArgs() {
  console.log(arguments);       // The arguments object
  console.log(arguments.length);
  for (let i = 0; i < arguments.length; i++) {
    console.log(arguments[i]);
  }
}

logArgs("a", "b", "c");
// Output:
// Arguments(3) ['a', 'b', 'c', callee: ƒ, Symbol(Symbol.iterator): ƒ]
// 3
// a
// b
// c
```
**Note**: It's generally recommended to use rest parameters (`...`) in modern JavaScript (ES6+) instead of the `arguments` object for better clarity and because `arguments` doesn't work with arrow functions.

## 6. The `return` Statement <a name="return-statement"></a>
The `return` statement specifies the value to be returned by the function. When a `return` statement is executed, the function immediately stops executing and the specified value is returned to the caller.

```javascript
function calculateArea(width, height) {
  if (width <= 0 || height <= 0) {
    return "Invalid dimensions"; // Early return for invalid input
  }
  return width * height; // Returns the calculated area
}

let area1 = calculateArea(5, 10); // 50
let area2 = calculateArea(-2, 5); // "Invalid dimensions"

console.log(area1);
console.log(area2);
```
If a function does not have a `return` statement, or has a `return` statement without a value, it implicitly returns `undefined`.

```javascript
function doNothing() {
  // No return statement
}
let result = doNothing();
console.log(result); // undefined
```

## 7. Function Scope <a name="function-scope"></a>
Scope determines the accessibility (visibility) of variables. JavaScript has global scope, local (function) scope, and block scope (for variables declared with `let` and `const`).

### Global Scope <a name="global-scope"></a>
Variables declared outside any function are in the global scope and can be accessed from anywhere in your code.
```javascript
let globalVar = "I am global";

function showGlobal() {
  console.log(globalVar);
}
showGlobal(); // I am global
```

### Local (Function) Scope <a name="local-scope"></a>
Variables declared inside a function (with `var`, `let`, or `const`) are in the local scope of that function. They are only accessible from within that function.
```javascript
function myFunction() {
  let localVar = "I am local";
  console.log(localVar);
}

myFunction(); // I am local
// console.log(localVar); // Error: localVar is not defined (outside its scope)
```
Each function call creates a new scope.

### Block Scope (with `let` and `const`) <a name="block-scope-functions"></a>
Variables declared with `let` and `const` inside a block (e.g., within `{}` of an `if` statement or a `for` loop) are scoped to that block.
```javascript
function testBlockScope() {
  if (true) {
    let blockVar = "I am in a block";
    const blockConst = "Me too!";
    console.log(blockVar);    // I am in a block
    console.log(blockConst);  // Me too!
  }
  // console.log(blockVar); // Error: blockVar is not defined
  // console.log(blockConst); // Error: blockConst is not defined
}
testBlockScope();
```
(A more detailed discussion of scope and closures will be in a later tutorial.)

## 8. Hoisting <a name="hoisting-functions"></a>
Hoisting is JavaScript's default behavior of moving declarations to the top of their current scope (script or function) before code execution.

### Function Declarations Hoisting <a name="declaration-hoisting"></a>
Function declarations are fully hoisted. This means you can call a function declaration before it appears in the code.
```javascript
hoistedGreet(); // Works!

function hoistedGreet() {
  console.log("Hello from a hoisted declaration!");
}
```

### Function Expressions Hoisting <a name="expression-hoisting"></a>
Function expressions are not fully hoisted. If you use `var` to declare the variable holding the function expression, the variable declaration (`var expressionGreet;`) is hoisted, but its assignment (the function itself) is not. If you use `let` or `const`, the variable is hoisted but remains in the "temporal dead zone" until the declaration is encountered.

```javascript
// Using var
// expressionGreetVar(); // TypeError: expressionGreetVar is not a function
var expressionGreetVar = function() {
  console.log("Hello from a var expression!");
};
expressionGreetVar(); // Works

// Using let
// expressionGreetLet(); // ReferenceError: Cannot access 'expressionGreetLet' before initialization
let expressionGreetLet = function() {
  console.log("Hello from a let expression!");
};
expressionGreetLet(); // Works
```
Arrow functions, being expressions, follow the same hoisting rules as function expressions.

## 9. Immediately Invoked Function Expressions (IIFE) <a name="iife"></a>
An IIFE is a function that is defined and executed immediately after its creation. It's a common pattern for creating a local scope to avoid polluting the global scope.

**Syntax:**
```javascript
(function() {
  // Code here runs immediately
  let privateVar = "I am private";
  console.log("IIFE executed! " + privateVar);
})();

// With parameters
(function(name) {
  console.log("Hello, " + name + " from an IIFE!");
})("IIFE User");

// Arrow function IIFE
(() => {
  console.log("Arrow IIFE executed!");
})();
```
IIFEs were more common before ES6 modules provided a standard way to manage scope and dependencies.

## 10. Functions as First-Class Citizens <a name="first-class-functions"></a>
In JavaScript, functions are "first-class citizens." This means functions are treated like any other value (e.g., numbers, strings, objects):
*   They can be assigned to variables and properties of objects.
*   They can be passed as arguments to other functions.
*   They can be returned as values from other functions.

```javascript
function sayHi() {
  return "Hi!";
}
let fn = sayHi; // Assign function to a variable
console.log(fn()); // Hi!

function operate(operationFunc, a, b) { // Pass function as argument
  return operationFunc(a, b);
}
function addOp(x, y) { return x + y; }
console.log(operate(addOp, 5, 3)); // 8

function createGreeter(greeting) { // Return function from function
  return function(name) {
    console.log(`${greeting}, ${name}!`);
  };
}
const greetHello = createGreeter("Hello");
greetHello("World"); // Hello, World!
```
This first-class nature enables powerful patterns like higher-order functions and closures.

## 11. Callbacks (Brief Introduction) <a name="callbacks-intro"></a>
A callback function is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action.

```javascript
function fetchData(url, callback) {
  // Simulate fetching data
  console.log(`Fetching from ${url}...`);
  setTimeout(function() {
    const data = { message: "Data received!" };
    callback(data); // Execute the callback with the data
  }, 1000);
}

function displayData(data) {
  console.log(data.message);
}

fetchData("/api/data", displayData); // Pass displayData as a callback
// Output (after ~1 second):
// Fetching from /api/data...
// Data received!
```
Callbacks are fundamental for asynchronous operations in JavaScript.

## 12. Best Practices for Functions <a name="best-practices-functions"></a>
*   **Single Responsibility Principle**: Functions should ideally do one thing well.
*   **Descriptive Names**: Choose clear, descriptive names for functions and parameters.
*   **Keep Functions Small**: Shorter functions are easier to understand, test, and maintain.
*   **Avoid Side Effects**: When possible, make functions pure (given the same input, always return the same output, and have no side effects like modifying external state).
*   **Limit Parameters**: Functions with too many parameters can be hard to use. Consider using an object to pass multiple related values.
*   **Add Comments**: Explain complex logic or non-obvious behavior.
*   **Use `const` for Function Expressions**: If a function expression assigned to a variable will not be reassigned, declare it with `const`.

## 13. Key Takeaways <a name="key-takeaways-functions"></a>
*   Functions are reusable blocks of code for performing specific tasks.
*   They can be defined as declarations, expressions, or arrow functions.
*   Parameters are placeholders in the definition; arguments are values passed during invocation.
*   `return` specifies the value a function sends back; defaults to `undefined`.
*   Functions create their own local scope.
*   Function declarations are hoisted; expressions are not (fully).
*   Arrow functions offer concise syntax and lexical `this`.
*   IIFEs create private scopes and execute immediately.
*   JavaScript treats functions as first-class citizens, enabling powerful patterns like callbacks and higher-order functions.

Functions are the backbone of JavaScript programming, enabling structure, reusability, and abstraction.

---

⬅️ [Back to JavaScript Loops](../05-js-loops/README.md) | ➡️ [Next Section: Scope and Closures](../07-js-scope-closures/README.md)
