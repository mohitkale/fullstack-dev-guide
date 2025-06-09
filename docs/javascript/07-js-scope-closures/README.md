# 07. JavaScript Scope and Closures

Scope and closures are fundamental concepts in JavaScript that determine the accessibility of variables and functions in different parts of your code. Understanding them is crucial for writing robust, maintainable, and bug-free JavaScript applications.

## Table of Contents
1.  [What is Scope?](#what-is-scope)
    *   [Why is Scope Important?](#why-scope-important)
2.  [Types of Scope in JavaScript](#types-of-scope)
    *   [Global Scope](#global-scope-detail)
    *   [Function (Local) Scope](#function-scope-detail)
    *   [Block Scope (ES6 with `let` and `const`)](#block-scope-detail)
3.  [Lexical Scoping (Static Scope)](#lexical-scoping)
    *   [Scope Chain](#scope-chain)
4.  [Hoisting Revisited](#hoisting-revisited-scope)
    *   [Variable Hoisting (`var`, `let`, `const`)](#variable-hoisting-scope)
    *   [Function Hoisting (Declarations vs. Expressions)](#function-hoisting-scope)
5.  [What is a Closure?](#what-is-closure)
    *   [How Closures Work](#how-closures-work)
    *   [Key Characteristics of Closures](#closure-characteristics)
6.  [Practical Examples and Use Cases of Closures](#closure-examples)
    *   [Data Privacy / Encapsulation (Private Variables and Methods)](#closure-privacy)
    *   [Function Factories (Creating Functions with Preset Parameters)](#closure-factories)
    *   [Callbacks and Asynchronous Operations](#closure-callbacks-async)
    *   [Event Handlers](#closure-event-handlers)
    *   [Currying](#closure-currying)
    *   [Memoization](#closure-memoization)
7.  [Closures and Loops: Common Pitfalls](#closures-loops-pitfall)
    *   [The Problem with `var` in Loops](#var-in-loops-closure)
    *   [Solutions (`let` or IIFE)](#solutions-loop-closure)
8.  [Performance Considerations with Closures](#closure-performance)
9.  [Key Takeaways](#key-takeaways-scope-closures)

## 1. What is Scope? <a name="what-is-scope"></a>
Scope in JavaScript refers to the context in which variables and functions are declared and can be accessed. It defines the visibility and lifetime of variables and parameters.

### Why is Scope Important? <a name="why-scope-important"></a>
*   **Security**: Restricts access to variables, preventing unauthorized modification.
*   **Reduced Naming Collisions**: Allows using the same variable names in different scopes without conflict.
*   **Memory Management**: Variables defined in a scope are typically garbage-collected when the scope is exited (unless closures are involved).
*   **Modularity**: Helps in organizing code into logical blocks.

## 2. Types of Scope in JavaScript <a name="types-of-scope"></a>

### Global Scope <a name="global-scope-detail"></a>
Variables declared outside any function or block reside in the global scope. They are accessible from anywhere in your JavaScript code.
```javascript
let globalVar = "I am global";

function checkGlobal() {
  console.log(globalVar); // Accessible
}
checkGlobal();
console.log(globalVar); // Accessible
```
In a browser environment, global variables declared with `var` become properties of the `window` object. Variables declared with `let` and `const` at the top level are also global but are not added to the `window` object.

**Caution**: Overuse of global variables can lead to naming conflicts and make code harder to manage. It's generally a good practice to minimize global variables.

### Function (Local) Scope <a name="function-scope-detail"></a>
Variables declared inside a function (using `var`, `let`, or `const`) are local to that function. They can only be accessed from within that function and any functions nested inside it.
```javascript
function myFunction() {
  var functionVar = "I am local to myFunction (var)";
  let functionLet = "I am local to myFunction (let)";
  console.log(functionVar);
  console.log(functionLet);

  function nestedFunction() {
    console.log(functionVar); // Accessible from nested function
    console.log(functionLet); // Accessible from nested function
  }
  nestedFunction();
}

myFunction();
// console.log(functionVar); // Error: functionVar is not defined
// console.log(functionLet); // Error: functionLet is not defined
```
Each function call creates a new local scope.

### Block Scope (ES6 with `let` and `const`) <a name="block-scope-detail"></a>
ES6 introduced `let` and `const`, which allow for block-scoped variables. A block is any section of code enclosed in curly braces `{}`, such as in `if` statements, `for` loops, or standalone blocks.
```javascript
if (true) {
  let blockLet = "I am block-scoped (let)";
  const blockConst = "I am block-scoped (const)";
  var blockVar = "I am function-scoped (var), not block-scoped!";
  console.log(blockLet);
  console.log(blockConst);
  console.log(blockVar);
}

// console.log(blockLet);   // Error: blockLet is not defined
// console.log(blockConst); // Error: blockConst is not defined
console.log(blockVar);     // "I am function-scoped (var), not block-scoped!" (if this code is in a function or global scope)
```
Variables declared with `var` are not block-scoped; they are function-scoped or global-scoped.

## 3. Lexical Scoping (Static Scope) <a name="lexical-scoping"></a>
JavaScript uses lexical scoping (also known as static scope). This means that the scope of a variable is determined by its position in the source code at the time the code is written (lexed/parsed), not at runtime.

An inner function has access to the variables of its outer function(s), even after the outer function has returned. This is the foundation of closures.

```javascript
function outer() {
  let outerVar = "I am from outer function";

  function inner() {
    // inner function can access outerVar due to lexical scoping
    console.log(outerVar);
  }

  inner();
}

outer(); // Output: I am from outer function
```

### Scope Chain <a name="scope-chain"></a>
When a variable is accessed, JavaScript starts looking for it in the current scope. If not found, it moves up to the enclosing (parent) scope, and so on, until it reaches the global scope. This chain of scopes is called the scope chain.

If the variable is not found anywhere in the scope chain, a `ReferenceError` is thrown.

## 4. Hoisting Revisited <a name="hoisting-revisited-scope"></a>
Hoisting is JavaScript's behavior of moving declarations to the top of their respective scopes before code execution.

### Variable Hoisting (`var`, `let`, `const`) <a name="variable-hoisting-scope"></a>
*   **`var`**: Declarations are hoisted, and they are initialized with `undefined`. You can access them before the actual declaration line without an error (though the value will be `undefined`).
    ```javascript
    console.log(myVar); // undefined (hoisted, initialized to undefined)
    var myVar = 10;
    console.log(myVar); // 10
    ```
*   **`let` and `const`**: Declarations are hoisted, but they are *not* initialized. Accessing them before the declaration line results in a `ReferenceError`. This period between the start of the scope and the declaration is called the "Temporal Dead Zone" (TDZ).
    ```javascript
    // console.log(myLet); // ReferenceError: Cannot access 'myLet' before initialization (TDZ)
    let myLet = 20;
    console.log(myLet); // 20
    ```

### Function Hoisting (Declarations vs. Expressions) <a name="function-hoisting-scope"></a>
*   **Function Declarations**: The entire function (name and body) is hoisted. You can call them before they are defined in the code.
    ```javascript
    greet(); // "Hello from declaration!"
    function greet() {
      console.log("Hello from declaration!");
    }
    ```
*   **Function Expressions (including Arrow Functions)**: Only the variable declaration part is hoisted (if using `var`, `let`, or `const`). The function assignment itself is not. Attempting to call it before assignment results in an error (`TypeError` if `var`, `ReferenceError` if `let`/`const`).
    ```javascript
    // sayHiVar(); // TypeError: sayHiVar is not a function
    var sayHiVar = function() { console.log("Hi from var expression!"); };

    // sayHiLet(); // ReferenceError: Cannot access 'sayHiLet' before initialization
    let sayHiLet = () => { console.log("Hi from let arrow function!"); };
    ```

## 5. What is a Closure? <a name="what-is-closure"></a>
A closure is a combination of a function and the lexical environment (scope) within which that function was declared. This means a closure gives you access to an outer function's scope from an inner function, even after the outer function has finished executing and its scope is otherwise gone.

In simpler terms: **A function remembers the variables from the place where it is defined, regardless of where it is later executed.**

### How Closures Work <a name="how-closures-work"></a>
When a function is defined inside another function, the inner function forms a closure. It "closes over" the variables of its outer function.

```javascript
function createGreeter(greeting) {
  // 'greeting' is part of the lexical environment of 'greet'
  return function greet(name) {
    // 'greet' is a closure. It remembers 'greeting'.
    console.log(`${greeting}, ${name}!`);
  };
}

// Call createGreeter. It returns the 'greet' function.
// The 'greet' function forms a closure, capturing 'greeting = "Hello"'.
const sayHello = createGreeter("Hello");

// Call createGreeter again. It returns another 'greet' function.
// This new 'greet' function forms a new closure, capturing 'greeting = "Hi"'.
const sayHi = createGreeter("Hi");

sayHello("Alice"); // Output: Hello, Alice!
                   // 'sayHello' (the inner 'greet' function) still has access to 'greeting = "Hello"'
                   // from its lexical environment when createGreeter("Hello") was called.

sayHi("Bob");     // Output: Hi, Bob!
                 // 'sayHi' (the inner 'greet' function) has access to 'greeting = "Hi"'.
```

### Key Characteristics of Closures <a name="closure-characteristics"></a>
*   **Access to Outer Scope Variables**: Closures can access variables from their own scope, their outer function's scope, and the global scope.
*   **State Preservation**: Closures can maintain state between function calls because they keep a reference to their outer variables.
*   **Created at Function Definition**: Closures are created every time a function is defined, not when it's executed (though the captured variables get their values at execution time of the outer function).

## 6. Practical Examples and Use Cases of Closures <a name="closure-examples"></a>

### Data Privacy / Encapsulation (Private Variables and Methods) <a name="closure-privacy"></a>
Closures can be used to create private variables and methods, emulating encapsulation.
```javascript
function createCounter() {
  let count = 0; // 'count' is a private variable, not accessible from outside

  return {
    increment: function() {
      count++;
      console.log(count);
    },
    decrement: function() {
      count--;
      console.log(count);
    },
    getCount: function() {
      return count;
    }
  };
}

const counter1 = createCounter();
counter1.increment(); // 1
counter1.increment(); // 2
// console.log(counter1.count); // undefined (cannot access 'count' directly)
console.log("Current count:", counter1.getCount()); // Current count: 2

const counter2 = createCounter(); // Creates a new, independent counter
counter2.increment(); // 1
```

### Function Factories (Creating Functions with Preset Parameters) <a name="closure-factories"></a>
Closures allow you to create functions that are pre-configured with certain parameters.
```javascript
function multiplyBy(factor) {
  return function(number) {
    return number * factor;
  };
}

const double = multiplyBy(2);
const triple = multiplyBy(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15
```

### Callbacks and Asynchronous Operations <a name="closure-callbacks-async"></a>
Closures are essential for callbacks, especially in asynchronous operations, to maintain context.
```javascript
function processDataAsync(data, callback) {
  let processId = Math.floor(Math.random() * 1000);
  console.log(`Starting process ${processId} for data: ${data}`);

  setTimeout(function() {
    // This anonymous function is a closure.
    // It has access to 'data' and 'processId' from its outer scope (processDataAsync).
    let result = data.toUpperCase();
    console.log(`Process ${processId} completed. Result: ${result}`);
    callback(result);
  }, 1000);
}

processDataAsync("hello", function(processedResult) {
  console.log(`Callback received: ${processedResult}`);
});
```

### Event Handlers <a name="closure-event-handlers"></a>
When setting up event handlers, closures help maintain state or context specific to each handler.
```javascript
// Hypothetical example for a browser environment
/*
function setupButton(buttonId, message) {
  const button = document.getElementById(buttonId);
  button.addEventListener('click', function() {
    // This event listener function is a closure.
    // It remembers the 'message' specific to this button setup.
    console.log(message);
  });
}

setupButton('btn1', 'Button 1 was clicked!');
setupButton('btn2', 'Button 2 says hi!');
*/
```

### Currying <a name="closure-currying"></a>
Currying is a technique of transforming a function with multiple arguments into a sequence of functions, each taking a single argument. Closures make currying possible.
```javascript
function curryAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

console.log(curryAdd(1)(2)(3)); // 6
const add5 = curryAdd(5);
const add5and10 = add5(10);
console.log(add5and10(15)); // 30
```

### Memoization <a name="closure-memoization"></a>
Closures can be used to cache the results of expensive function calls.
```javascript
function memoize(fn) {
  const cache = {}; // This cache is part of the closure
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache[key]) {
      console.log("Fetching from cache...");
      return cache[key];
    }
    console.log("Calculating result...");
    const result = fn(...args);
    cache[key] = result;
    return result;
  };
}

function slowAdd(a, b) {
  // Simulate slow operation
  for (let i = 0; i < 1e8; i++) {}
  return a + b;
}

const memoizedAdd = memoize(slowAdd);
console.log(memoizedAdd(3, 4)); // Calculating result... 7
console.log(memoizedAdd(3, 4)); // Fetching from cache... 7
console.log(memoizedAdd(5, 6)); // Calculating result... 11
```

## 7. Closures and Loops: Common Pitfalls <a name="closures-loops-pitfall"></a>
A common pitfall occurs when creating closures inside loops with `var`.

### The Problem with `var` in Loops <a name="var-in-loops-closure"></a>
Variables declared with `var` are function-scoped, not block-scoped. If you create functions (closures) inside a loop that reference a `var`-declared loop variable, all those functions will refer to the *same* variable, which will have its final value after the loop finishes.

```javascript
for (var i = 1; i <= 3; i++) {
  setTimeout(function() {
    // This function is a closure. It captures 'i'.
    // However, 'i' is function-scoped (or global if this loop is global).
    // By the time these timeouts execute, the loop has finished and 'i' is 4.
    console.log(i); // Will print 4, three times
  }, 100);
}
```

### Solutions (`let` or IIFE) <a name="solutions-loop-closure"></a>
1.  **Using `let` (ES6)**: `let` is block-scoped. Each loop iteration creates a new scope for `i`, so each closure captures a different `i`.
    ```javascript
    for (let i = 1; i <= 3; i++) { // 'let' creates a new 'i' for each iteration
      setTimeout(function() {
        console.log(i); // Prints 1, then 2, then 3
      }, 100);
    }
    ```
2.  **Using an IIFE (Immediately Invoked Function Expression) (Pre-ES6)**: Create a new scope for each iteration by wrapping the closure creation in an IIFE.
    ```javascript
    for (var i = 1; i <= 3; i++) {
      (function(capturedI) {
        setTimeout(function() {
          console.log(capturedI); // Prints 1, then 2, then 3
        }, 100);
      })(i); // Pass current 'i' to the IIFE
    }
    ```

## 8. Performance Considerations with Closures <a name="closure-performance"></a>
*   **Memory Usage**: Closures keep references to their outer scope variables. This means these variables cannot be garbage-collected as long as the closure exists. If many closures are created, or if they hold references to large objects, it can increase memory consumption.
*   **Scope Chain Lookup**: Accessing variables from outer scopes can be slightly slower than accessing local variables, as JavaScript needs to traverse the scope chain. However, modern JavaScript engines are highly optimized, and this is rarely a significant bottleneck for most applications.

Generally, the benefits of closures (modularity, data privacy, powerful patterns) outweigh the minor performance considerations for typical use cases. Be mindful if creating a very large number of long-lived closures or closures capturing large data structures.

## 9. Key Takeaways <a name="key-takeaways-scope-closures"></a>
*   **Scope** defines where variables and functions are accessible.
*   JavaScript has **global, function, and block scopes**.
*   **Lexical scoping** means scope is determined at author-time, not runtime.
*   A **closure** is a function bundled with references to its surrounding state (lexical environment).
*   Closures allow inner functions to access outer function variables, even after the outer function has completed.
*   Closures enable powerful patterns like **data encapsulation, function factories, callbacks, currying, and memoization**.
*   Be aware of the common pitfall with closures in loops using `var`; use `let` or an IIFE to solve it.
*   While closures have slight performance implications, their benefits usually make them indispensable.

Understanding scope and closures is a hallmark of an proficient JavaScript developer, enabling more sophisticated and robust code.

---

⬅️ [Back to JavaScript Functions](../06-js-functions/README.md) | ➡️ [Next Section: Objects and Prototypes](../08-js-objects-prototypes/README.md)
