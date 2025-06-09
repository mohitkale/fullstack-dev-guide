# 40. TypeScript and Functional Programming Concepts

Functional Programming (FP) is a programming paradigm that treats computation as the evaluation of mathematical functions and avoids changing-state and mutable data. TypeScript, with its strong type system, can significantly enhance the practice of functional programming by providing compile-time checks for purity, immutability, and function signatures.

This tutorial explores key functional programming concepts and how they can be applied in TypeScript.

## Table of Contents
1.  [Core Principles of Functional Programming](#core-fp-principles)
    *   Pure Functions
    *   Immutability
    *   First-Class and Higher-Order Functions
    *   Referential Transparency
    *   Side Effect Minimization
2.  [Pure Functions in TypeScript](#pure-functions-ts)
    *   Definition and benefits (predictability, testability, memoization)
    *   Examples of pure and impure functions
    *   Using `readonly` to enforce input immutability (shallow)
3.  [Immutability in TypeScript](#immutability-ts)
    *   `readonly` keyword for properties
    *   `Readonly<T>` utility type for objects
    *   `ReadonlyArray<T>` utility type for arrays
    *   Techniques for immutable updates (spread syntax, `Object.assign`, array methods like `map`, `filter`, `reduce`)
    *   Libraries for deep immutability (e.g., Immer, Immutable.js)
4.  [First-Class and Higher-Order Functions](#first-class-higher-order-ts)
    *   **First-Class Functions:** Treating functions as values (assigning to variables, passing as arguments, returning from other functions).
    *   **Higher-Order Functions (HOFs):** Functions that operate on other functions (take functions as arguments or return them).
        *   Examples: `map`, `filter`, `reduce`, custom HOFs.
        *   Typing HOFs in TypeScript.
5.  [Function Composition](#function-composition-ts)
    *   Combining multiple functions to create a new one.
    *   `pipe` and `compose` utility functions (implementing and typing them).
6.  [Currying and Partial Application](#currying-partial-application-ts)
    *   **Currying:** Transforming a function with multiple arguments into a sequence of functions, each taking a single argument.
    *   **Partial Application:** Creating a new function by pre-filling some of the arguments of an existing function.
    *   Typing curried and partially applied functions.
7.  [Algebraic Data Types (ADTs) - Conceptual Overview](#adts-ts)
    *   Sum Types (e.g., Discriminated Unions)
    *   Product Types (e.g., Objects, Tuples)
    *   How TypeScript's type system supports ADT-like patterns.
8.  [Handling Side Effects](#handling-side-effects-fp-ts)
    *   Isolating side effects from pure logic.
    *   Using types to represent effects (e.g., `IO` monad concept from libraries).
9.  [Functional Data Structures (Brief Overview)](#functional-data-structures-ts)
    *   Persistent data structures.
    *   Libraries like `Immutable.js`.
10. [Popular Functional Programming Libraries in TypeScript Ecosystem](#fp-libraries-ts)
    *   [**fp-ts**](#fp-ts-library)
        *   A library for typed functional programming in TypeScript.
        *   Provides `Option`, `Either`, `Task`, `IO`, and other functional constructs.
    *   [**Ramda**](#ramda-library)
        *   A practical functional library for JavaScript programmers (with good TypeScript typings).
        *   Automatic currying, data-last functions.
    *   [**Lodash/fp**](#lodash-fp-library)
        *   The functional programming variant of Lodash.
11. [Benefits of FP with TypeScript](#benefits-fp-ts)
12. [Challenges and Considerations](#challenges-fp-ts)
13. [Key Takeaways](#key-takeaways-fp-ts)

## 1. Core Principles of Functional Programming <a name="core-fp-principles"></a>
*   **Pure Functions:** Output depends only on input, no side effects.
*   **Immutability:** Data structures are not changed after creation.
*   **First-Class Functions:** Functions are treated like any other variable.
*   **Higher-Order Functions:** Functions can take other functions as arguments or return them.
*   **Referential Transparency:** An expression can be replaced with its corresponding value without changing the program's behavior.
*   **Side Effect Minimization:** Interactions with the outside world (I/O, global state mutation) are carefully controlled.

## 2. Pure Functions in TypeScript <a name="pure-functions-ts"></a>
A pure function:
1.  Given the same input, will always return the same output.
2.  Produces no side effects (e.g., no network requests, DOM manipulation, or mutation of external state).

```typescript
// Pure function
function add(a: number, b: number): number {
  return a + b;
}

// Impure function (mutates external state)
let total = 0;
function addToTotal(x: number): void {
  total += x; // Side effect: modifies 'total'
  console.log(x); // Side effect: logs to console
}

// Pure function with object input (shallow immutability enforced by readonly)
interface Point { readonly x: number; readonly y: number; }
function translatePoint(point: Point, dx: number, dy: number): Point {
  return { x: point.x + dx, y: point.y + dy };
}
```

## 3. Immutability in TypeScript <a name="immutability-ts"></a>
TypeScript provides `readonly` and utility types for shallow immutability.

```typescript
// readonly property
interface User {
  readonly id: number;
  name: string;
}
const user: User = { id: 1, name: 'Alice' };
// user.id = 2; // Error: Cannot assign to 'id' because it is a read-only property.
user.name = 'Alicia'; // OK

// Readonly<T>
const frozenUser: Readonly<User> = { id: 2, name: 'Bob' };
// frozenUser.name = 'Robert'; // Error
// frozenUser.id = 3; // Error

// ReadonlyArray<T>
const numbers: ReadonlyArray<number> = [1, 2, 3];
// numbers.push(4); // Error: Property 'push' does not exist on type 'readonly number[]'.
// numbers[0] = 0; // Error: Index signature in type 'readonly number[]' only permits reading.

// Immutable update for an array
const newNumbers = [...numbers, 4]; // Creates a new array

// Immutable update for an object
const updatedUser: User = { ...user, name: 'Alice Smith' };
```
For deep immutability, consider libraries like Immer or Immutable.js.

## 4. First-Class and Higher-Order Functions <a name="first-class-higher-order-ts"></a>
```typescript
// First-class function: assigned to a variable
const greet = (name: string): string => `Hello, ${name}!`;

// Higher-order function: takes a function as an argument
function operate(a: number, b: number, operation: (x: number, y: number) => number): number {
  return operation(a, b);
}
const sum = operate(5, 3, (x, y) => x + y); // 8
const product = operate(5, 3, (x, y) => x * y); // 15

// Higher-order function: returns a function
function multiplyBy(factor: number): (value: number) => number {
  return (value: number) => value * factor;
}
const double = multiplyBy(2);
const triple = multiplyBy(3);
console.log(double(5)); // 10
console.log(triple(5)); // 15
```

## 5. Function Composition <a name="function-composition-ts"></a>
Combining functions such that the output of one becomes the input of the next.

```typescript
// pipe: f(g(h(x)))
function pipe<T extends any[], R1, R2, R3>(
  f1: (...args: T) => R1,
  f2: (a: R1) => R2,
  f3: (a: R2) => R3
): (...args: T) => R3 {
  return (...args: T) => f3(f2(f1(...args)));
}

// compose: h(g(f(x))) - reverse order of pipe
function compose<T extends any[], R1, R2, R3>(
  f3: (a: R2) => R3, 
  f2: (a: R1) => R2, 
  f1: (...args: T) => R1
): (...args: T) => R3 {
  return (...args: T) => f3(f2(f1(...args)));
}

const add5 = (x: number): number => x + 5;
const multiplyBy2 = (x: number): number => x * 2;
const stringify = (x: number): string => `Result: ${x}`;

const processWithPipe = pipe(add5, multiplyBy2, stringify);
console.log(processWithPipe(10)); // Result: 30 ( (10+5)*2 )

const processWithCompose = compose(stringify, multiplyBy2, add5);
console.log(processWithCompose(10)); // Result: 30
```
More generic `pipe` and `compose` implementations can handle varying numbers of functions.

## 6. Currying and Partial Application <a name="currying-partial-application-ts"></a>

**Currying:**
```typescript
function addCurried(a: number): (b: number) => (c: number) => number {
  return (b: number) => (c: number) => a + b + c;
}
const sumCurried = addCurried(1)(2)(3); // 6
const add1 = addCurried(1);
const add1and2 = add1(2);
const finalSum = add1and2(3); // 6

// Generic currying (simplified example)
function curry<A, B, C, R>(f: (a: A, b: B, c: C) => R): (a: A) => (b: B) => (c: C) => R {
  return (a: A) => (b: B) => (c: C) => f(a, b, c);
}
```

**Partial Application:**
```typescript
function logMessage(level: string, timestamp: Date, message: string): void {
  console.log(`[${level.toUpperCase()}] ${timestamp.toISOString()}: ${message}`);
}

function partial<T1, T2, T3, R>(
  fn: (a: T1, b: T2, c: T3) => R,
  arg1: T1
): (b: T2, c: T3) => R {
  return (b: T2, c: T3) => fn(arg1, b, c);
}

const errorLogger = partial(logMessage, 'error');
errorLogger(new Date(), 'Something went wrong!');
// Output: [ERROR] <timestamp>: Something went wrong!
```

## 7. Algebraic Data Types (ADTs) - Conceptual Overview <a name="adts-ts"></a>
TypeScript's type system naturally supports patterns similar to ADTs.
*   **Sum Types (Tagged/Discriminated Unions):** Represent a value that can be one of several types. See Tutorial #11 (Advanced Types).
    ```typescript
    type Shape = 
      | { kind: 'circle'; radius: number }
      | { kind: 'square'; sideLength: number };
    ```
*   **Product Types (Objects/Interfaces, Tuples):** Represent a value that is a combination of other types.
    ```typescript
    interface Point { x: number; y: number; } // Object
    type PointTuple = [number, number]; // Tuple
    ```

## 8. Handling Side Effects <a name="handling-side-effects-fp-ts"></a>
In pure FP, side effects are often managed by wrapping them in types (like `IO` monads in `fp-ts`) that represent the effectful computation. The execution of these effects is deferred to the 'edge' of the application.

```typescript
// Conceptual example with fp-ts IO
import { IO, io } from 'fp-ts/IO';

const logIO = (message: string): IO<void> => {
  return () => console.log(message); // The effect is wrapped
};

const program: IO<void> = io.chain(logIO('Hello'), () => logIO('World'));

// To run the program and execute effects:
// program(); 
```

## 9. Functional Data Structures (Brief Overview) <a name="functional-data-structures-ts"></a>
These are data structures designed with immutability in mind. Operations on them return new instances instead of modifying the original. `Immutable.js` is a popular library providing such structures (List, Map, Set, etc.).

## 10. Popular Functional Programming Libraries in TypeScript Ecosystem <a name="fp-libraries-ts"></a>

### fp-ts <a name="fp-ts-library"></a>
*   Provides common functional programming constructs like `Option` (for nullable values), `Either` (for error handling), `Task` (for async operations), `IO`, and more, all with strong typing.
*   Steeper learning curve but very powerful for building robust applications.

### Ramda <a name="ramda-library"></a>
*   Functions are automatically curried.
*   Data is typically the last argument, facilitating function composition.
*   Has good TypeScript typings available via `@types/ramda`.

### Lodash/fp <a name="lodash-fp-library"></a>
*   A functional version of Lodash, with fixed-arity, auto-curried, data-last functions.

## 11. Benefits of FP with TypeScript <a name="benefits-fp-ts"></a>
*   **Predictability & Reliability:** Pure functions and immutability make code easier to reason about.
*   **Testability:** Pure functions are easy to unit test.
*   **Concurrency:** Immutability reduces issues with shared state in concurrent environments.
*   **Reusability & Composability:** Small, pure functions can be easily combined.
*   **Enhanced by Types:** TypeScript catches errors related to function signatures, immutability (with `readonly`), and data flow at compile time.

## 12. Challenges and Considerations <a name="challenges-fp-ts"></a>
*   **Learning Curve:** FP concepts can be unfamiliar.
*   **Performance:** Naive immutable updates can sometimes lead to performance overhead (though often optimized by engines or libraries).
*   **Verbosity:** Some FP patterns can be more verbose without utility libraries.
*   **Integration:** Mixing FP with imperative or OOP code requires care.

## 13. Key Takeaways <a name="key-takeaways-fp-ts"></a>
*   TypeScript's type system (especially `readonly`, utility types, and discriminated unions) strongly supports functional programming principles.
*   Pure functions and immutability are cornerstones of FP, leading to more robust and testable code.
*   Higher-order functions, composition, and currying are powerful techniques for abstracting and reusing logic.
*   Libraries like `fp-ts` and `Ramda` can provide robust tools and utilities for practicing FP in TypeScript.
*   Adopting FP concepts, even partially, can significantly improve code quality in TypeScript projects.

---

⬅️ [Back to Advanced Error Handling and Debugging in TypeScript](../39-ts-advanced-error-handling/README.md) | ➡️ [Next: TypeScript with Serverless Architectures](../41-ts-serverless/README.md) *(Placeholder)*
