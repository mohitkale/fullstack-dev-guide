# 20. JavaScript Best Practices & Coding Conventions

Writing JavaScript is one thing; writing clean, maintainable, efficient, and understandable JavaScript is another. This tutorial outlines essential best practices and common coding conventions that help produce high-quality JavaScript code. Adhering to these guidelines can significantly improve collaboration, reduce bugs, and make your code easier to manage over time.

## Table of Contents
1.  [Code Readability and Formatting](#code-readability)
    *   [Consistent Indentation](#indentation)
    *   [Line Length](#line-length)
    *   [Whitespace (Spaces vs. Tabs, Around Operators)](#whitespace)
    *   [Braces and Parentheses](#braces-parentheses)
    *   [Naming Conventions](#naming-conventions)
        *   Variables and Functions (camelCase)
        *   Constants (UPPER_SNAKE_CASE)
        *   Classes (PascalCase/UpperCamelCase)
        *   Private Properties/Methods (e.g., `_privateField` - by convention)
    *   [Comments (When and How)](#comments)
2.  [Variable Declarations](#variable-declarations)
    *   [Prefer `const` by Default](#prefer-const)
    *   [Use `let` for Reassignable Variables](#use-let)
    *   [Avoid `var`](#avoid-var)
    *   [Declare Variables at the Top of Their Scope](#declare-at-top)
3.  [Strict Mode (`'use strict';`)](#strict-mode)
4.  [Type Checking and Coercion](#type-checking-coercion)
    *   [Use Strict Equality (`===`, `!==`)](#strict-equality)
    *   [Understand Truthy and Falsy Values](#truthy-falsy)
    *   [Explicit Type Conversions When Necessary](#explicit-conversion)
5.  [Functions and Modularity](#functions-modularity)
    *   [Keep Functions Small and Focused (Single Responsibility Principle)](#srp-functions)
    *   [Use Descriptive Function Names](#descriptive-function-names)
    *   [Minimize Side Effects](#minimize-side-effects)
    *   [Use Pure Functions When Possible](#pure-functions)
    *   [Avoid Too Many Parameters](#function-parameters)
    *   [Use Default Parameters](#default-parameters-recap)
6.  [Error Handling](#error-handling-bp)
    *   [Use `try...catch...finally` for Synchronous Errors](#try-catch-finally-bp)
    *   [Handle Promise Rejections (`.catch()` or `try...catch` with `async/await`)](#promise-rejections-bp)
    *   [Throw Meaningful Errors](#throw-meaningful-errors)
    *   [Avoid Swallowing Errors](#avoid-swallowing-errors)
7.  [Asynchronous Code](#async-code-bp)
    *   [Prefer Promises and `async/await` Over Callbacks](#prefer-promises-async)
    *   [Manage Concurrency (e.g., `Promise.all`, `Promise.race`)](#manage-concurrency)
8.  [Code Structure and Organization](#code-structure)
    *   [Use Modules (`import`/`export`)](#use-modules-bp)
    *   [Group Related Code](#group-related-code)
    *   [Directory Structure Conventions](#directory-structure)
9.  [Performance Considerations](#performance-bp)
    *   [Minimize DOM Manipulations](#minimize-dom)
    *   [Optimize Loops](#optimize-loops)
    *   [Be Mindful of Algorithm Complexity](#algorithm-complexity)
    *   [Debounce and Throttle Event Handlers](#debounce-throttle)
    *   [Avoid Unnecessary Computations](#avoid-unnecessary-computations)
10. [Security Best Practices](#security-bp)
    *   [Sanitize User Input (Prevent XSS)](#sanitize-input)
    *   [Avoid `eval()` and `new Function(string)`](#avoid-eval)
    *   [Use HTTPS](#use-https)
    *   [Manage Dependencies (Keep them updated, be aware of vulnerabilities)](#manage-dependencies)
11. [Using Linters and Formatters](#linters-formatters)
    *   [ESLint](#eslint)
    *   [Prettier](#prettier)
12. [Version Control (Git)](#version-control)
13. [Testing](#testing-bp)
    *   [Unit Tests](#unit-tests)
    *   [Integration Tests](#integration-tests)
    *   [End-to-End (E2E) Tests](#e2e-tests)
14. [Key Takeaways](#key-takeaways-best-practices)

## 1. Code Readability and Formatting <a name="code-readability"></a>
Readable code is easier to understand, debug, and maintain.

### Consistent Indentation <a name="indentation"></a>
Use a consistent number of spaces (commonly 2 or 4) or tabs for indentation. Do not mix them. Most style guides recommend spaces.

### Line Length <a name="line-length"></a>
Keep lines reasonably short (e.g., 80-120 characters) to improve readability and avoid horizontal scrolling.

### Whitespace (Spaces vs. Tabs, Around Operators) <a name="whitespace"></a>
*   Use spaces around operators (`=`, `+`, `-`, `*`, `/`, `===`, etc.) for clarity.
    ```javascript
    const sum = a + b; // Good
    const sum=a+b;   // Bad
    ```
*   Use spaces after commas in argument lists, array literals, and object literals.

### Braces and Parentheses <a name="braces-parentheses"></a>
*   Always use curly braces `{}` for `if`, `else`, `for`, `while` blocks, even if they are single-line, to prevent ambiguity.
    ```javascript
    if (condition) { // Good
      doSomething();
    }
    // if (condition) doSomething(); // Bad, error-prone
    ```
*   Be consistent with brace style (e.g., K&R style - opening brace on the same line, or Allman style - opening brace on a new line).

### Naming Conventions <a name="naming-conventions"></a>
*   **Variables and Functions**: Use `camelCase` (e.g., `userName`, `calculateTotal`).
*   **Constants**: Use `UPPER_SNAKE_CASE` for values that are truly constant and global (e.g., `MAX_USERS`, `API_KEY`).
*   **Classes and Constructor Functions**: Use `PascalCase` or `UpperCamelCase` (e.g., `UserService`, `class Person {}`).
*   **Private Properties/Methods (Convention)**: JavaScript doesn't have true private members (though `#privateFields` are a new feature). A common convention is to prefix with an underscore `_` (e.g., `_internalValue`, `_doInternalWork()`). This signals intent but doesn't enforce privacy.

### Comments (When and How) <a name="comments"></a>
*   Write comments to explain *why* something is done, not *what* is being done (the code should explain the *what*).
*   Explain complex logic, workarounds, or important decisions.
*   Keep comments up-to-date with code changes.
*   Use JSDoc for documenting functions, classes, and modules, especially in libraries or larger projects.
    ```javascript
    /**
     * Calculates the sum of two numbers.
     * @param {number} a - The first number.
     * @param {number} b - The second number.
     * @returns {number} The sum of a and b.
     */
    function add(a, b) {
      return a + b;
    }
    ```

## 2. Variable Declarations <a name="variable-declarations"></a>

### Prefer `const` by Default <a name="prefer-const"></a>
Use `const` for variables that will not be reassigned. This makes the code easier to reason about and prevents accidental reassignments.

### Use `let` for Reassignable Variables <a name="use-let"></a>
Use `let` only when you know a variable's value needs to change (e.g., loop counters, state variables).

### Avoid `var` <a name="avoid-var"></a>
`var` has function scope (or global scope) and hoisting behaviors that can lead to confusion and bugs. `let` and `const` have block scope and are generally safer.

### Declare Variables at the Top of Their Scope <a name="declare-at-top"></a>
Declare variables at the beginning of the block (or function) where they are used. This improves readability by showing all variables used in that scope upfront.

## 3. Strict Mode (`'use strict';`) <a name="strict-mode"></a>
Enable strict mode at the beginning of your scripts or functions. It helps catch common coding errors and prevents or throws errors for certain unsafe actions.
```javascript
'use strict';
// Your code here
```
ES6 modules are implicitly in strict mode.

## 4. Type Checking and Coercion <a name="type-checking-coercion"></a>

### Use Strict Equality (`===`, `!==`) <a name="strict-equality"></a>
Always use strict equality operators to avoid unexpected type coercion issues that can occur with loose equality (`==`, `!=`).

### Understand Truthy and Falsy Values <a name="truthy-falsy"></a>
Be aware of JavaScript's truthy/falsy values (falsy: `false`, `0`, `''`, `null`, `undefined`, `NaN`). Use them intentionally in conditions, but be explicit if clarity is needed.

### Explicit Type Conversions When Necessary <a name="explicit-conversion"></a>
If you need to convert types, do it explicitly (e.g., `Number(value)`, `String(value)`, `Boolean(value)`) rather than relying on implicit coercion, which can be obscure.

## 5. Functions and Modularity <a name="functions-modularity"></a>

### Keep Functions Small and Focused (Single Responsibility Principle) <a name="srp-functions"></a>
Each function should do one thing well. This makes them easier to understand, test, and reuse.

### Use Descriptive Function Names <a name="descriptive-function-names"></a>
Function names should clearly indicate what the function does (e.g., `getUserById`, `calculateTotalPrice`).

### Minimize Side Effects <a name="minimize-side-effects"></a>
A side effect is any modification of state outside the function's own scope (e.g., modifying global variables, DOM manipulation). While sometimes necessary, try to isolate and minimize them.

### Use Pure Functions When Possible <a name="pure-functions"></a>
A pure function:
1.  Given the same input, always returns the same output.
2.  Has no side effects.
Pure functions are easier to test and reason about.

### Avoid Too Many Parameters <a name="function-parameters"></a>
Functions with many parameters can be hard to use. Consider passing an object as a single parameter if a function needs multiple inputs.
```javascript
// Less ideal
// function createUser(username, email, age, isAdmin, isActive) { ... }

// Better
function createUser({ username, email, age, isAdmin = false, isActive = true }) {
  // ...
}
createUser({ username: 'test', email: 't@ex.com', age: 30 });
```

### Use Default Parameters <a name="default-parameters-recap"></a>
Use default parameter values (ES6) to make functions more robust and easier to call.

## 6. Error Handling <a name="error-handling-bp"></a>

### Use `try...catch...finally` for Synchronous Errors <a name="try-catch-finally-bp"></a>
Properly handle potential errors in synchronous code.

### Handle Promise Rejections (`.catch()` or `try...catch` with `async/await`) <a name="promise-rejections-bp"></a>
Always handle potential rejections in asynchronous operations using Promises.

### Throw Meaningful Errors <a name="throw-meaningful-errors"></a>
When throwing errors, use standard `Error` objects (or custom error classes inheriting from `Error`) and provide descriptive messages.
```javascript
if (userId <= 0) {
  throw new Error('User ID must be a positive number.');
}
```

### Avoid Swallowing Errors <a name="avoid-swallowing-errors"></a>
Do not catch errors and then ignore them (e.g., an empty `catch` block). At least log the error.

## 7. Asynchronous Code <a name="async-code-bp"></a>

### Prefer Promises and `async/await` Over Callbacks <a name="prefer-promises-async"></a>
They provide better readability and error handling for asynchronous operations, avoiding "callback hell."

### Manage Concurrency <a name="manage-concurrency"></a>
Use `Promise.all()` when you need to wait for multiple asynchronous operations to complete. Use `Promise.race()` when you need the result of the first operation to complete.

## 8. Code Structure and Organization <a name="code-structure"></a>

### Use Modules (`import`/`export`) <a name="use-modules-bp"></a>
Organize your code into reusable modules. This improves maintainability and scalability.

### Group Related Code <a name="group-related-code"></a>
Keep related functions, classes, and variables together within modules or files.

### Directory Structure Conventions <a name="directory-structure"></a>
Follow a consistent and logical directory structure for your project (e.g., `src/`, `components/`, `utils/`, `services/`, `tests/`).

## 9. Performance Considerations <a name="performance-bp"></a>

### Minimize DOM Manipulations <a name="minimize-dom"></a>
DOM operations are expensive. Batch updates, use document fragments, or leverage virtual DOM libraries (like React, Vue) where appropriate.

### Optimize Loops <a name="optimize-loops"></a>
*   Avoid unnecessary work inside loops.
*   Cache array lengths if they are accessed repeatedly in a loop condition.
*   Choose the right loop type for the task.

### Be Mindful of Algorithm Complexity <a name="algorithm-complexity"></a>
Understand the Big O notation of your algorithms, especially when dealing with large datasets.

### Debounce and Throttle Event Handlers <a name="debounce-throttle"></a>
For frequently firing events (like `scroll`, `resize`, `mousemove`), use debouncing or throttling to limit the rate at which event handlers are executed.

### Avoid Unnecessary Computations <a name="avoid-unnecessary-computations"></a>
Cache results of expensive computations if they are needed multiple times (memoization).

## 10. Security Best Practices <a name="security-bp"></a>

### Sanitize User Input (Prevent XSS) <a name="sanitize-input"></a>
Never trust user input. Always sanitize or escape data before rendering it in the DOM to prevent Cross-Site Scripting (XSS) attacks.

### Avoid `eval()` and `new Function(string)` <a name="avoid-eval"></a>
These can execute arbitrary code and pose significant security risks. They also hinder performance and make debugging harder.

### Use HTTPS <a name="use-https"></a>
Always serve your web applications over HTTPS to encrypt data in transit.

### Manage Dependencies <a name="manage-dependencies"></a>
*   Keep your project dependencies (npm packages, libraries) up-to-date to patch known vulnerabilities.
*   Use tools like `npm audit` or Snyk to check for vulnerabilities.

## 11. Using Linters and Formatters <a name="linters-formatters"></a>

### ESLint <a name="eslint"></a>
A pluggable and configurable linter tool for identifying and reporting on patterns in JavaScript. Helps enforce coding standards and catch errors early.

### Prettier <a name="prettier"></a>
An opinionated code formatter. It enforces a consistent style by parsing your code and re-printing it with its own rules.

Using ESLint and Prettier together can automate code quality and style consistency.

## 12. Version Control (Git) <a name="version-control"></a>
*   Use Git for version control.
*   Make small, atomic commits with clear commit messages.
*   Use branches for new features and bug fixes.
*   Regularly pull changes from the remote repository.

## 13. Testing <a name="testing-bp"></a>
Write tests to ensure your code behaves as expected and to prevent regressions.

### Unit Tests <a name="unit-tests"></a>
Test individual functions or components in isolation.

### Integration Tests <a name="integration-tests"></a>
Test how different parts of your application work together.

### End-to-End (E2E) Tests <a name="e2e-tests"></a>
Test the entire application flow from the user's perspective.

## 14. Key Takeaways <a name="key-takeaways-best-practices"></a>
*   **Consistency is Key**: Whatever conventions you choose, apply them consistently.
*   **Readability Matters**: Write code for humans first, computers second.
*   **Simplicity**: Prefer simple solutions over complex ones unless necessary.
*   **Automate**: Use linters, formatters, and automated tests to maintain quality.
*   **Continuous Learning**: Stay updated with JavaScript evolution and emerging best practices.

Adopting these best practices will lead to more robust, maintainable, and collaborative JavaScript projects.

---

⬅️ [Back to ES6+ Features Overview](../19-js-es6-features-summary/README.md) | ➡️ [Next Section: JavaScript Design Patterns](../21-js-design-patterns/README.md) (Placeholder)
