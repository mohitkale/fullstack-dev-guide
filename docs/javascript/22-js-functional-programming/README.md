# 22. Functional Programming Concepts in JavaScript

Functional Programming (FP) is a programming paradigm that treats computation as the evaluation of mathematical functions and avoids changing-state and mutable data. JavaScript, being a multi-paradigm language, supports functional programming concepts very well. Adopting FP principles can lead to more predictable, maintainable, and testable code.

## Table of Contents
1.  [What is Functional Programming?](#what-is-fp)
    *   [Core Principles](#core-principles-fp)
2.  [Pure Functions](#pure-functions-fp)
    *   [Characteristics](#pure-characteristics)
    *   [Benefits](#pure-benefits)
    *   [Examples](#pure-examples)
3.  [Immutability](#immutability)
    *   [What is Immutability?](#what-is-immutability)
    *   [Achieving Immutability in JavaScript](#achieving-immutability)
        *   Primitives
        *   Objects and Arrays (Techniques: `Object.assign`, spread syntax, `concat`, `slice`, `filter`, `map`, `reduce`)
        *   Libraries (e.g., Immer, Immutable.js)
    *   [Benefits of Immutability](#immutability-benefits)
4.  [First-Class Functions](#first-class-functions)
    *   [Definition](#first-class-definition)
    *   [Examples in JavaScript](#first-class-examples)
5.  [Higher-Order Functions (HOFs)](#higher-order-functions)
    *   [Definition](#hof-definition)
    *   [Functions as Arguments](#hof-args-example)
    *   [Functions as Return Values](#hof-return-example)
    *   [Common HOFs: `map`, `filter`, `reduce`](#common-hofs-recap)
6.  [Function Composition](#function-composition)
    *   [Concept](#composition-concept)
    *   [Example](#composition-example)
7.  [Currying](#currying)
    *   [Concept](#currying-concept)
    *   [Example](#currying-example)
8.  [Partial Application](#partial-application)
    *   [Concept](#partial-application-concept)
    *   [Difference from Currying](#partial-vs-currying)
    *   [Example](#partial-application-example)
9.  [Referential Transparency](#referential-transparency)
10. [Side Effects](#side-effects-fp)
    *   [Minimizing and Isolating Side Effects](#minimizing-side-effects-fp)
11. [Benefits of Functional Programming in JavaScript](#benefits-of-fp-js)
12. [Challenges and Considerations](#challenges-fp-js)
13. [Key Takeaways](#key-takeaways-fp)

## 1. What is Functional Programming? <a name="what-is-fp"></a>
Functional Programming (FP) is a declarative programming paradigm where programs are constructed by applying and composing functions. It emphasizes:
*   **Expressions over Statements**: FP prefers expressions that evaluate to a value rather than statements that perform actions.
*   **Avoiding Shared State**: Minimizing or eliminating shared state that can be modified by different parts of the program.
*   **Avoiding Mutable Data**: Data structures are typically immutable, meaning their state cannot change after creation.
*   **Avoiding Side Effects**: Functions ideally only compute a result based on their inputs and don't interact with the outside world (e.g., modifying global variables, I/O).

### Core Principles <a name="core-principles-fp"></a>
*   Pure Functions
*   Immutability
*   First-Class and Higher-Order Functions
*   Function Composition

## 2. Pure Functions <a name="pure-functions-fp"></a>

### Characteristics <a name="pure-characteristics"></a>
A function is considered "pure" if it meets two main criteria:
1.  **Deterministic**: Given the same set of inputs, it will always return the same output.
2.  **No Side Effects**: Its execution does not cause any observable side effects. This means it doesn't modify any external state (e.g., global variables, objects passed by reference that are mutated), perform I/O operations, or call other functions with side effects.

### Benefits <a name="pure-benefits"></a>
*   **Predictable**: Easy to reason about because their output depends solely on their input.
*   **Testable**: Simple to test as you only need to provide inputs and check outputs.
*   **Cacheable/Memoizable**: Results can be cached for given inputs.
*   **Parallelizable**: Can be run in parallel without fear of race conditions.

### Examples <a name="pure-examples"></a>
```javascript
// Pure function
function add(a, b) {
  return a + b;
}
console.log(add(2, 3)); // Always 5

// Impure function (modifies external state)
let total = 0;
function addToTotal(value) {
  total += value; // Side effect: modifies 'total'
  return total;
}
console.log(addToTotal(5)); // 5
console.log(addToTotal(5)); // 10 (output changes for same input if called sequentially)

// Impure function (depends on external state)
const y = 10;
function addY(x) {
  return x + y; // Depends on external 'y'
}
console.log(addY(5)); // 15. If 'y' changes, output changes.
```

## 3. Immutability <a name="immutability"></a>

### What is Immutability? <a name="what-is-immutability"></a>
An object is immutable if its state cannot be changed after it's created. If you need to modify an immutable object, you create a new object with the changes, leaving the original unchanged.

### Achieving Immutability in JavaScript <a name="achieving-immutability"></a>
*   **Primitives**: String, Number, Boolean, Null, Undefined, Symbol, BigInt are inherently immutable.
    ```javascript
    let str = "hello";
    let newStr = str.toUpperCase(); // Creates a new string "HELLO"
    console.log(str); // "hello" (original is unchanged)
    console.log(newStr); // "HELLO"
    ```
*   **Objects and Arrays**: These are mutable by default. To work with them immutably:
    *   **`Object.assign()` or Spread Syntax (`...`) for Objects**:
        ```javascript
        const person = { name: "Alice", age: 30 };
        const updatedPerson = { ...person, age: 31 }; // New object
        // const updatedPerson = Object.assign({}, person, { age: 31 });
        console.log(person); // { name: "Alice", age: 30 }
        console.log(updatedPerson); // { name: "Alice", age: 31 }
        ```
    *   **Spread Syntax (`...`), `concat()`, `slice()`, `filter()`, `map()`, `reduce()` for Arrays**:
        ```javascript
        const numbers = [1, 2, 3];
        const newNumbers = [...numbers, 4]; // [1, 2, 3, 4]
        const numbersPlusOne = numbers.map(n => n + 1); // [2, 3, 4]
        const evenNumbers = numbers.filter(n => n % 2 === 0); // [2]
        console.log(numbers); // [1, 2, 3] (original unchanged in map/filter)
        ```
        Avoid methods like `push`, `pop`, `splice`, `sort` directly on original arrays if immutability is desired. Create copies first.
*   **Libraries**: For complex applications, libraries like Immer or Immutable.js can help manage immutable state more efficiently.

### Benefits of Immutability <a name="immutability-benefits"></a>
*   **Predictability**: State changes are explicit, making it easier to track and debug.
*   **Change Detection**: Easier to detect changes (e.g., in UI frameworks like React) by comparing object references.
*   **Concurrency**: Safer in multi-threaded environments as data doesn't change unexpectedly.
*   **Undo/Redo**: Simplifies implementing features like undo/redo by keeping previous states.

## 4. First-Class Functions <a name="first-class-functions"></a>

### Definition <a name="first-class-definition"></a>
In a language with first-class functions, functions are treated like any other variable. This means:
*   They can be assigned to variables.
*   They can be passed as arguments to other functions.
*   They can be returned as values from other functions.
*   They can be stored in data structures (like arrays or objects).

### Examples in JavaScript <a name="first-class-examples"></a>
JavaScript treats functions as first-class citizens.
```javascript
// Assign to a variable
const greet = function(name) {
  console.log(`Hello, ${name}!`);
};
greet("Alice");

// Pass as an argument
function operateOnNumber(num, operation) {
  return operation(num);
}
const double = x => x * 2;
console.log(operateOnNumber(5, double)); // 10

// Return from another function
function createGreeter(greeting) {
  return function(name) {
    console.log(`${greeting}, ${name}!`);
  };
}
const sayHello = createGreeter("Hello");
sayHello("Bob"); // Hello, Bob!
```

## 5. Higher-Order Functions (HOFs) <a name="higher-order-functions"></a>

### Definition <a name="hof-definition"></a>
A higher-order function is a function that either:
1.  Takes one or more functions as arguments, OR
2.  Returns a function as its result.

First-class functions are a prerequisite for higher-order functions.

### Functions as Arguments <a name="hof-args-example"></a>
```javascript
const numbers = [1, 2, 3, 4];
const isEven = num => num % 2 === 0;

const evenNumbers = numbers.filter(isEven); // .filter is a HOF, isEven is passed as an argument
console.log(evenNumbers); // [2, 4]
```

### Functions as Return Values <a name="hof-return-example"></a>
```javascript
function multiplyBy(factor) {
  return function(number) { // Returns a new function
    return number * factor;
  };
}

const double = multiplyBy(2);
const triple = multiplyBy(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15
```

### Common HOFs: `map`, `filter`, `reduce` <a name="common-hofs-recap"></a>
These array methods are prime examples of HOFs in JavaScript, allowing for powerful, declarative data transformations.
(Covered in [Tutorial 07: JavaScript Arrays & Array Methods](../07-js-arrays-array-methods/README.md))

## 6. Function Composition <a name="function-composition"></a>

### Concept <a name="composition-concept"></a>
Function composition is the process of combining two or more functions to produce a new function. The output of one function becomes the input of the next.
If you have `f(x)` and `g(x)`, composing them might look like `h(x) = f(g(x))`.

### Example <a name="composition-example"></a>
```javascript
const add5 = x => x + 5;
const multiplyBy2 = x => x * 2;

// Compose manually
const composedManually = x => add5(multiplyBy2(x));
console.log(composedManually(10)); // (10 * 2) + 5 = 25

// Generic compose function (right-to-left execution)
const compose = (...fns) => x => fns.reduceRight((acc, fn) => fn(acc), x);

const composedFunction = compose(add5, multiplyBy2);
console.log(composedFunction(10)); // 25

// Another common compose (or pipe for left-to-right)
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x);
const pipedFunction = pipe(multiplyBy2, add5);
console.log(pipedFunction(10)); // 25
```

## 7. Currying <a name="currying"></a>

### Concept <a name="currying-concept"></a>
Currying is the technique of transforming a function that takes multiple arguments into a sequence of functions, each taking a single argument.
For example, a function `f(a, b, c)` can be curried into `f(a)(b)(c)`.

### Example <a name="currying-example"></a>
```javascript
// Non-curried function
const add = (a, b, c) => a + b + c;
console.log(add(1, 2, 3)); // 6

// Curried function
const curriedAdd = a => b => c => a + b + c;
console.log(curriedAdd(1)(2)(3)); // 6

const add1 = curriedAdd(1);
const add1and2 = add1(2);
console.log(add1and2(3)); // 6
console.log(add1and2(10)); // 13
```
Currying helps create specialized functions and makes function composition easier.

## 8. Partial Application <a name="partial-application"></a>

### Concept <a name="partial-application-concept"></a>
Partial application refers to the process of fixing a number of arguments to a function, producing another function of smaller arity (number of arguments).

### Difference from Currying <a name="partial-vs-currying"></a>
*   **Currying** always produces a sequence of unary functions (functions taking one argument).
*   **Partial Application** produces a function that can take any number of remaining arguments.

### Example <a name="partial-application-example"></a>
```javascript
// Original function
function multiply(a, b, c) {
  return a * b * c;
}

// Partially apply 'a' using bind
const multiplyBy5 = multiply.bind(null, 5); // 'this' is null, 'a' is 5
console.log(multiplyBy5(2, 3)); // 5 * 2 * 3 = 30

// Partially apply 'a' and 'b'
const multiplyBy5And2 = multiply.bind(null, 5, 2); // 'a' is 5, 'b' is 2
console.log(multiplyBy5And2(3)); // 5 * 2 * 3 = 30

// Custom partial application function
function partial(fn, ...fixedArgs) {
  return function(...remainingArgs) {
    return fn(...fixedArgs, ...remainingArgs);
  };
}

const partialMultiplyBy10 = partial(multiply, 10);
console.log(partialMultiplyBy10(2, 4)); // 10 * 2 * 4 = 80
```

## 9. Referential Transparency <a name="referential-transparency"></a>
An expression is referentially transparent if it can be replaced with its corresponding value without changing the program's behavior. Pure functions are referentially transparent.
```javascript
// add(2, 3) is referentially transparent because it can be replaced with 5.
const result = add(2, 3) + 10; // Same as: const result = 5 + 10;
```
This property makes code easier to reason about and optimize.

## 10. Side Effects <a name="side-effects-fp"></a>
Side effects are any interactions a function has with the outside world beyond returning a value. Examples:
*   Modifying a global variable or an object passed by reference.
*   Console logging.
*   DOM manipulation.
*   Network requests (AJAX/Fetch).
*   Writing to a file or database.

### Minimizing and Isolating Side Effects <a name="minimizing-side-effects-fp"></a>
While pure FP aims to eliminate side effects, real-world applications need them. The strategy is to:
1.  Keep most of your logic in pure functions.
2.  Isolate side effects at the boundaries of your application (e.g., in specific modules or functions that handle I/O).

## 11. Benefits of Functional Programming in JavaScript <a name="benefits-of-fp-js"></a>
*   **Improved Readability**: Declarative style often makes code easier to understand.
*   **Predictability**: Pure functions and immutability reduce bugs caused by unexpected state changes.
*   **Testability**: Pure functions are easy to unit test.
*   **Maintainability**: Code becomes more modular and easier to refactor.
*   **Concurrency**: Immutability and pure functions simplify parallel execution.
*   **Reusability**: Small, pure functions are highly reusable and composable.

## 12. Challenges and Considerations <a name="challenges-fp-js"></a>
*   **Performance**: Creating new objects/arrays for immutability can have performance overhead in some cases, especially with large data structures. (Often mitigated by structural sharing or optimized libraries).
*   **Learning Curve**: The concepts can be initially challenging for developers accustomed to imperative or OOP styles.
*   **Verbosity**: Sometimes, achieving strict purity or immutability can lead to more verbose code if not handled with appropriate abstractions or libraries.
*   **Debugging**: Stack traces can sometimes be deeper with many HOFs or compositions, though pure functions themselves are easier to debug individually.

## 13. Key Takeaways <a name="key-takeaways-fp"></a>
*   Functional Programming emphasizes pure functions, immutability, and function composition.
*   JavaScript's first-class functions make it well-suited for FP techniques.
*   Higher-order functions like `map`, `filter`, and `reduce` are powerful tools for data transformation.
*   Techniques like currying and partial application enhance function reusability.
*   Adopting FP principles can lead to cleaner, more robust, and maintainable JavaScript code.
*   It's not an all-or-nothing approach; you can incrementally introduce FP concepts into your projects.

---

⬅️ [Back to JavaScript Design Patterns](../21-js-design-patterns/README.md) | ➡️ [Next Section: JavaScript Modules In-Depth (Revisiting ES6 Modules & CommonJS)](../23-js-modules-advanced/README.md) (Placeholder)
