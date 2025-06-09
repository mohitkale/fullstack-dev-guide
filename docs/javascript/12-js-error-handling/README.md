# 12. Error Handling in JavaScript

Errors are an inevitable part of programming. Effective error handling is crucial for building robust, reliable, and user-friendly JavaScript applications. It allows your program to gracefully manage unexpected situations, provide meaningful feedback, and prevent crashes.

## Table of Contents
1.  [What is Error Handling?](#what-is-error-handling)
2.  [Types of Errors in JavaScript](#types-of-errors)
    *   [Syntax Errors](#syntax-errors)
    *   [Runtime Errors (Exceptions)](#runtime-errors)
    *   [Logical Errors](#logical-errors)
3.  [The `try...catch...finally` Statement](#try-catch-finally)
    *   [`try` Block](#try-block)
    *   [`catch` Block](#catch-block)
    *   [`finally` Block](#finally-block)
    *   [Optional `catch` Binding](#optional-catch-binding)
4.  [The `Error` Object](#error-object)
    *   [Properties (`name`, `message`, `stack`)](#error-object-properties)
    *   [Built-in Error Types](#built-in-error-types)
        *   `SyntaxError`
        *   `ReferenceError`
        *   `TypeError`
        *   `RangeError`
        *   `URIError`
        *   `EvalError` (rarely used)
5.  [Throwing Errors (`throw` statement)](#throwing-errors)
    *   [Throwing `Error` Objects](#throwing-error-objects)
    *   [Creating Custom Error Classes](#custom-error-classes)
6.  [Error Handling in Asynchronous Code](#async-error-handling)
    *   [Error Handling with Callbacks](#callbacks-error-handling)
    *   [Error Handling with Promises (`.catch()`)](#promises-error-handling)
    *   [Error Handling with `async/await` (`try...catch`)](#async-await-error-handling-detail)
7.  [Global Error Handling](#global-error-handling)
    *   [In Browsers: `window.onerror`](#window-onerror)
    *   [In Browsers: `window.onunhandledrejection`](#window-onunhandledrejection)
    *   [In Node.js: `process.on('uncaughtException')`](#node-uncaughtexception)
    *   [In Node.js: `process.on('unhandledRejection')`](#node-unhandledrejection)
8.  [Best Practices for Error Handling](#error-handling-best-practices)
9.  [Key Takeaways](#key-takeaways-error-handling)

## 1. What is Error Handling? <a name="what-is-error-handling"></a>
Error handling is the process of anticipating, detecting, and responding to errors or exceptional conditions that occur during program execution. Instead of letting an error halt the program abruptly, error handling mechanisms allow you to manage the situation, log information, inform the user, or attempt recovery.

## 2. Types of Errors in JavaScript <a name="types-of-errors"></a>

### Syntax Errors <a name="syntax-errors"></a>
These occur when the code violates the grammatical rules of JavaScript. The JavaScript engine parses the code before execution, and if it encounters a syntax error, the script won't run at all.
```javascript
// let x = 5; y = 10; // SyntaxError: Missing semicolon or unexpected token
// function myFunction( {} // SyntaxError: Unexpected end of input
```
Syntax errors must be fixed by the developer before the code can be executed.

### Runtime Errors (Exceptions) <a name="runtime-errors"></a>
These errors occur during the execution of the code, after it has been successfully parsed. They are also known as exceptions. Examples include trying to call a method on an undefined variable, dividing by zero (though JavaScript returns `Infinity` for this, other operations can cause issues), or accessing a non-existent property.
```javascript
let x;
// console.log(x.toUpperCase()); // TypeError: Cannot read properties of undefined (reading 'toUpperCase')

function divide(a, b) {
  if (b === 0) {
    throw new Error("Division by zero is not allowed.");
  }
  return a / b;
}
// divide(10, 0); // Error: Division by zero is not allowed.
```
Runtime errors can be caught and handled using `try...catch` blocks.

### Logical Errors <a name="logical-errors"></a>
These are errors in the program's logic. The code runs without throwing syntax or runtime errors, but it produces incorrect results. Logical errors are often the hardest to find and fix because the program appears to be working.
```javascript
function calculateArea(radius) {
  return Math.PI * radius; // Logical Error: Should be radius * radius (or radius ** 2)
}
console.log(calculateArea(5)); // Incorrect result, but no JS error thrown
```
Error handling mechanisms like `try...catch` don't directly catch logical errors. They require debugging, testing, and careful code review.

## 3. The `try...catch...finally` Statement <a name="try-catch-finally"></a>
JavaScript provides the `try...catch...finally` statement to handle runtime errors (exceptions).

### `try` Block <a name="try-block"></a>
The code that might throw an error is placed within the `try` block.

### `catch` Block <a name="catch-block"></a>
If an exception occurs within the `try` block, the control immediately jumps to the `catch` block. The `catch` block receives an error object (often named `e`, `err`, or `error`) containing information about the error.

### `finally` Block <a name="finally-block"></a>
The `finally` block is optional. Its code executes after the `try` and `catch` blocks, regardless of whether an exception was thrown or caught. It's typically used for cleanup operations, like closing files or releasing resources.

```javascript
function processData(data) {
  try {
    console.log("Attempting to process data...");
    if (typeof data.value !== 'number') {
      throw new TypeError("Data value must be a number.");
    }
    let result = data.value * 2;
    console.log("Processed result:", result);
    return result;
  } catch (error) {
    console.error("An error occurred:", error.name, "-", error.message);
    // console.error("Stack trace:", error.stack);
    // return undefined; // Or handle the error in another way
  } finally {
    console.log("Processing attempt finished.");
  }
}

processData({ value: 10 });
// Output:
// Attempting to process data...
// Processed result: 20
// Processing attempt finished.

processData({ value: "abc" });
// Output:
// Attempting to process data...
// An error occurred: TypeError - Data value must be a number.
// Processing attempt finished.
```

### Optional `catch` Binding <a name="optional-catch-binding"></a>
Since ES2019, if you don't need the error object in the `catch` block, you can omit the binding:
```javascript
try {
  // Risky code
  riskyOperation();
} catch {
  // No error object needed, just handle the fact that an error occurred
  console.error("An unspecified error occurred during riskyOperation.");
}
```

## 4. The `Error` Object <a name="error-object"></a>
When an error occurs, JavaScript creates an `Error` object (or an instance of one of its subtypes).

### Properties (`name`, `message`, `stack`) <a name="error-object-properties"></a>
Standard `Error` objects have these key properties:
*   **`name`**: A string indicating the type of error (e.g., `"Error"`, `"TypeError"`, `"ReferenceError"`).
*   **`message`**: A string providing a human-readable description of the error.
*   **`stack`** (non-standard but widely supported): A string containing the call stack trace, showing where the error originated and the sequence of calls that led to it. Very useful for debugging.

```javascript
try {
  let num = undefinedVariable;
} catch (e) {
  console.log("Error Name:", e.name);         // ReferenceError
  console.log("Error Message:", e.message);   // undefinedVariable is not defined
  console.log("Error Stack:\n", e.stack);    // Stack trace string
}
```

### Built-in Error Types <a name="built-in-error-types"></a>
JavaScript has several built-in error constructors that inherit from `Error`:
*   **`SyntaxError`**: An error in parsing code (cannot be caught by `try...catch` as parsing happens before execution).
*   **`ReferenceError`**: Occurs when trying to access a variable that hasn't been declared.
*   **`TypeError`**: Occurs when a value is not of the expected type (e.g., calling a method on `null` or `undefined`, or treating a number as a function).
*   **`RangeError`**: Occurs when a numeric variable or parameter is outside of its valid range (e.g., `new Array(-1)`).
*   **`URIError`**: Occurs when global URI handling functions (like `encodeURIComponent()`, `decodeURIComponent()`) are used incorrectly.
*   **`EvalError`**: Historically for errors in `eval()`, but rarely used in modern JavaScript. `eval()` now typically throws other standard errors.
*   **`InternalError`** (non-standard, e.g., in Firefox): Indicates an error in the JavaScript engine itself (e.g., "too much recursion").

## 5. Throwing Errors (`throw` statement) <a name="throwing-errors"></a>
You can create and throw your own errors using the `throw` statement. This is useful for signaling exceptional conditions in your functions.

### Throwing `Error` Objects <a name="throwing-error-objects"></a>
It's best practice to throw instances of the `Error` object or its subtypes, as they automatically capture stack trace information.
```javascript
function validateAge(age) {
  if (typeof age !== 'number') {
    throw new TypeError("Age must be a number.");
  }
  if (age < 0 || age > 120) {
    throw new RangeError("Age must be between 0 and 120.");
  }
  console.log("Age is valid.");
}

try {
  validateAge(25);
  validateAge("young");
  // validateAge(150); // This line won't be reached if the previous one throws
} catch (error) {
  console.error(`Validation failed: ${error.name} - ${error.message}`);
}
```

### Creating Custom Error Classes <a name="custom-error-classes"></a>
For more specific error handling, you can create custom error classes by extending the built-in `Error` class.
```javascript
class NetworkError extends Error {
  constructor(message, statusCode) {
    super(message); // Call the parent constructor (Error)
    this.name = "NetworkError";
    this.statusCode = statusCode;
    // Error.captureStackTrace(this, this.constructor); // Optional: ensure correct stack trace in some environments
  }
}

function fetchData(url) {
  // Simulate a network request
  if (!url.startsWith("https://")) {
    throw new NetworkError("Request failed: Invalid URL protocol.", 400);
  }
  // ... fetch logic
  console.log("Data fetched successfully from", url);
}

try {
  fetchData("http://api.example.com");
} catch (error) {
  if (error instanceof NetworkError) {
    console.error(`Network Error (Status ${error.statusCode}): ${error.message}`);
  } else {
    console.error("An unexpected error occurred:", error.message);
  }
}
```

## 6. Error Handling in Asynchronous Code <a name="async-error-handling"></a>

### Error Handling with Callbacks <a name="callbacks-error-handling"></a>
The common convention for callbacks is the "error-first" pattern, where the first argument to the callback is an error object (or `null` if no error).
```javascript
function readFileAsync(filePath, callback) {
  // Simulate reading a file
  setTimeout(() => {
    if (filePath === "nonexistent.txt") {
      callback(new Error("File not found"), null);
    } else {
      callback(null, "File content here.");
    }
  }, 500);
}

readFileAsync("data.txt", (err, data) => {
  if (err) {
    console.error("Callback Error:", err.message);
    return;
  }
  console.log("File data:", data);
});
```

### Error Handling with Promises (`.catch()`) <a name="promises-error-handling"></a>
Promises have a built-in mechanism for error handling using the `.catch()` method. A `.catch()` block at the end of a promise chain can handle rejections from any preceding promise.
```javascript
function fetchDataPromise() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (Math.random() < 0.5) {
        resolve("Data fetched successfully!");
      } else {
        reject(new Error("Promise failed to fetch data."));
      }
    }, 500);
  });
}

fetchDataPromise()
  .then(data => {
    console.log(data);
    // return JSON.parse(data); // This could throw an error if data is not valid JSON
  })
  .catch(error => {
    console.error("Promise Error:", error.message);
  });
```

### Error Handling with `async/await` (`try...catch`) <a name="async-await-error-handling-detail"></a>
`async/await` allows you to use standard `try...catch` blocks for handling errors in asynchronous code, making it look more like synchronous error handling.
```javascript
async function processAsyncData() {
  try {
    console.log("Starting async data processing...");
    const data = await fetchDataPromise(); // fetchDataPromise is defined above
    console.log("Async/await success:", data);
    // const parsed = JSON.parse(data); // This could also throw
    // console.log("Parsed:", parsed);
  } catch (error) {
    console.error("Async/await Error:", error.message);
  }
}

processAsyncData();
```

## 7. Global Error Handling <a name="global-error-handling"></a>
Sometimes you want a centralized place to catch errors that weren't handled locally.

### In Browsers: `window.onerror` <a name="window-onerror"></a>
Catches most uncaught runtime errors in scripts.
```javascript
// window.onerror = function(message, source, lineno, colno, error) {
//   console.log("Global error caught by window.onerror:");
//   console.log("  Message:", message);
//   console.log("  Source:", source);
//   console.log("  Line:", lineno, "Column:", colno);
//   console.log("  Error Object:", error);
//   return true; // Prevents the default browser error handling (e.g., logging to console)
// };
//  throw new Error("Test global error handler");
```

### In Browsers: `window.onunhandledrejection` <a name="window-onunhandledrejection"></a>
Catches unhandled promise rejections.
```javascript
// window.onunhandledrejection = function(event) {
//   console.warn("Global unhandled promise rejection:", event.reason);
//   // event.preventDefault(); // Optional: prevent default logging
// };
// Promise.reject(new Error("Test unhandled rejection"));
```

### In Node.js: `process.on('uncaughtException')` <a name="node-uncaughtexception"></a>
Catches uncaught exceptions. Using this is generally discouraged for actual error handling as the application state might be corrupted. It's more for cleanup and logging before exiting.
```javascript
// process.on('uncaughtException', (err, origin) => {
//   console.error(`Node.js Uncaught Exception at: ${origin}, error: ${err.stack || err}`);
//   // Perform synchronous cleanup, then exit
//   // process.exit(1);
// });
// throw new Error("Node test uncaught exception");
```

### In Node.js: `process.on('unhandledRejection')` <a name="node-unhandledrejection"></a>
Catches unhandled promise rejections in Node.js.
```javascript
// process.on('unhandledRejection', (reason, promise) => {
//   console.error('Node.js Unhandled Rejection at:', promise, 'reason:', reason.stack || reason);
//   // Application specific logging, throwing an error, or other logic here
// });
// Promise.reject(new Error("Node test unhandled rejection"));
```

## 8. Best Practices for Error Handling <a name="error-handling-best-practices"></a>
*   **Be Specific**: Catch specific types of errors when possible, rather than generic `Error`.
*   **Fail Fast**: Identify and report errors as close to the source as possible.
*   **Provide Context**: Error messages should be clear and provide enough context to help diagnose the problem.
*   **Don't Swallow Errors**: Avoid empty `catch` blocks or `catch` blocks that only log to the console without re-throwing or properly handling the error, especially in library code.
*   **Use `finally` for Cleanup**: Ensure resources are released (e.g., close network connections, file handles) even if an error occurs.
*   **Throw `Error` Objects**: Always `throw new Error(...)` or instances of custom error classes, not strings or other types.
*   **Handle Asynchronous Errors Correctly**: Use `.catch()` for promises or `try...catch` with `async/await`.
*   **Log Errors Appropriately**: For debugging and monitoring, log errors with sufficient detail (stack trace, context).
*   **Inform the User Gracefully**: For user-facing applications, display user-friendly error messages instead of raw error details.
*   **Test Error Conditions**: Write tests to ensure your error handling logic works as expected.

## 9. Key Takeaways <a name="key-takeaways-error-handling"></a>
*   Error handling is essential for robust JavaScript applications.
*   `try...catch...finally` is the primary mechanism for synchronous error handling.
*   The `Error` object (and its subtypes) provides details about errors (`name`, `message`, `stack`).
*   Use `throw` to signal custom errors, preferably by throwing `Error` instances.
*   Asynchronous error handling requires specific patterns: error-first callbacks, promise `.catch()`, or `try...catch` with `async/await`.
*   Global error handlers can catch unhandled exceptions but should be used cautiously.
*   Following best practices leads to more maintainable and reliable code.

---

⬅️ [Back to JavaScript Modules](../11-js-modules/README.md) | ➡️ [Next Section: JSON (JavaScript Object Notation)](../13-js-json/README.md)
