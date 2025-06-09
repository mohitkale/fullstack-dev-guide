# 39. Advanced Error Handling and Debugging in TypeScript

Robust error handling and effective debugging are crucial for building reliable and maintainable TypeScript applications. While TypeScript's static type system helps prevent many common bugs, runtime errors are still inevitable. This tutorial dives into advanced techniques for managing errors and debugging TypeScript code.

## Table of Contents
1.  [Beyond Basic `Error` Objects](#beyond-basic-error)
    *   Limitations of the generic `Error` object
    *   The importance of specific error information
2.  [Creating Custom Error Classes](#custom-error-classes-ts)
    *   Extending the built-in `Error` class
    *   Adding custom properties (e.g., error codes, metadata)
    *   Ensuring `instanceof` works correctly (prototype chain considerations)
3.  [Discriminated Unions for Error Types](#discriminated-unions-errors-ts)
    *   Modeling different error states with a common discriminant property
    *   Exhaustive checking with `never` type in `switch` statements
4.  [Error Aggregation and `AggregateError`](#aggregate-error-ts)
    *   Handling multiple errors from operations like `Promise.allSettled`
    *   Using `AggregateError` to group errors
5.  [Advanced Asynchronous Error Handling](#async-error-handling-ts)
    *   `try...catch` with `async/await`
    *   `.catch()` with Promises
    *   Unhandled promise rejections: detection and handling
    *   Error propagation in async call stacks
6.  [Error Boundaries (Conceptual)](#error-boundaries-ts)
    *   Concept in UI frameworks (e.g., React Error Boundaries)
    *   Gracefully handling UI-related errors to prevent app crashes
7.  [Structured Logging with Types](#structured-logging-ts)
    *   Defining types for log entries
    *   Including context, error details, and stack traces
    *   Using logging libraries (e.g., Winston, Pino) with TypeScript
8.  [Debugging TypeScript Code](#debugging-ts)
    *   [**Source Maps**](#source-maps-debugging-ts)
        *   Understanding their role: mapping compiled JS back to TS source
        *   Configuring `sourceMap` and `inlineSourceMap` in `tsconfig.json`
    *   [**IDE Debuggers (e.g., VS Code)**](#ide-debuggers-ts)
        *   Setting up `launch.json` for Node.js and browser debugging
        *   Breakpoints, conditional breakpoints, logpoints
        *   Stepping through code (step over, step into, step out)
        *   Inspecting variables and call stacks
        *   Watch expressions
    *   [**Browser Developer Tools**](#browser-dev-tools-ts)
        *   Debugging client-side TypeScript via source maps
        *   Console methods beyond `console.log` (e.g., `console.error`, `console.trace`, `console.table`)
    *   [**Leveraging the Type System During Debugging**](#type-system-debugging-ts)
        *   Understanding expected types vs. actual runtime values
        *   Using type assertions carefully during debugging (e.g., in watch expressions)
9.  [Static Analysis and Linters](#static-analysis-linters-ts)
    *   ESLint with `@typescript-eslint/parser` and `@typescript-eslint/eslint-plugin`
    *   Custom lint rules for error handling patterns
10. [Using `never` for Exhaustiveness in Error Handling](#never-exhaustiveness-ts)
11. [Best Practices for Error Handling](#best-practices-error-handling-ts)
12. [Key Takeaways](#key-takeaways-ts-error-debug)

## 1. Beyond Basic `Error` Objects <a name="beyond-basic-error"></a>
The standard `Error` object (`new Error('Something went wrong')`) is often too generic. For effective error handling, you need more specific information about what went wrong, where, and why.

## 2. Creating Custom Error Classes <a name="custom-error-classes-ts"></a>
Extend `Error` to create more specific error types.
```typescript
class NetworkError extends Error {
  public readonly statusCode?: number;
  public readonly errorCode?: string;

  constructor(message: string, statusCode?: number, errorCode?: string) {
    super(message);
    this.name = 'NetworkError'; // Set the name property for better identification
    this.statusCode = statusCode;
    this.errorCode = errorCode;

    // Fix for instanceof not working correctly in some environments (e.g., older JS)
    Object.setPrototypeOf(this, NetworkError.prototype);
  }
}

class ValidationError extends Error {
  public readonly fieldErrors: Record<string, string[]>;

  constructor(message: string, fieldErrors: Record<string, string[]>) {
    super(message);
    this.name = 'ValidationError';
    this.fieldErrors = fieldErrors;
    Object.setPrototypeOf(this, ValidationError.prototype);
  }
}

// Usage:
try {
  // Simulate an API call
  throw new NetworkError('Failed to fetch data', 500, 'ERR_API_UNAVAILABLE');
} catch (error) {
  if (error instanceof NetworkError) {
    console.error(`Network Error: ${error.message}, Status: ${error.statusCode}`);
  } else if (error instanceof ValidationError) {
    console.error(`Validation Error: ${error.message}`, error.fieldErrors);
  } else if (error instanceof Error) {
    console.error(`Generic Error: ${error.message}`);
  } else {
    console.error('Unknown error:', error);
  }
}
```

## 3. Discriminated Unions for Error Types <a name="discriminated-unions-errors-ts"></a>
Useful for representing a set of known error states.
```typescript
type FileReadSuccess = { status: 'success'; content: string };
type FileReadNotFoundError = { status: 'error'; type: 'notFound'; path: string };
type FileReadPermissionError = { status: 'error'; type: 'permissionDenied'; path: string };
type FileReadUnknownError = { status: 'error'; type: 'unknown'; originalError: unknown };

export type FileReadResult = 
  | FileReadSuccess 
  | FileReadNotFoundError 
  | FileReadPermissionError 
  | FileReadUnknownError;

function processFileResult(result: FileReadResult) {
  if (result.status === 'success') {
    console.log('File content:', result.content);
  } else {
    // result.status is 'error'
    switch (result.type) {
      case 'notFound':
        console.error(`Error: File not found at ${result.path}`);
        break;
      case 'permissionDenied':
        console.error(`Error: Permission denied for file at ${result.path}`);
        break;
      case 'unknown':
        console.error('An unknown file read error occurred:', result.originalError);
        break;
      default:
        // Exhaustiveness check
        const _exhaustiveCheck: never = result;
        console.error('Unhandled error type:', _exhaustiveCheck);
    }
  }
}
```

## 4. Error Aggregation and `AggregateError` <a name="aggregate-error-ts"></a>
Introduced in ES2021, `AggregateError` is useful for representing multiple errors, often from parallel operations.
```typescript
async function processMultipleTasks() {
  try {
    const results = await Promise.allSettled([
      Promise.resolve('Success 1'),
      Promise.reject(new Error('Failure 1')),
      Promise.resolve('Success 2'),
      Promise.reject(new Error('Failure 2')),
    ]);

    const errors = results
      .filter(result => result.status === 'rejected')
      .map(result => (result as PromiseRejectedResult).reason);

    if (errors.length > 0) {
      throw new AggregateError(errors, 'Some tasks failed');
    }

    console.log('All tasks relevant to this block completed successfully or were handled.');

  } catch (error) {
    if (error instanceof AggregateError) {
      console.error(`Message: ${error.message}`);
      error.errors.forEach(indError => console.error('Individual error:', indError));
    } else {
      console.error('An unexpected error occurred:', error);
    }
  }
}
// processMultipleTasks();
```

## 5. Advanced Asynchronous Error Handling <a name="async-error-handling-ts"></a>
*   **`async/await`:** `try...catch` blocks work intuitively.
*   **Promises:** Use `.catch()` for error handling. Ensure all promises have a `.catch()` or are part of an `async` function with `try...catch` to avoid unhandled rejections.
*   **Unhandled Promise Rejections:** Node.js emits an `unhandledRejection` event. Browsers have a similar mechanism. It's crucial to have global handlers for these to log and potentially gracefully shut down or inform the user.

## 6. Error Boundaries (Conceptual) <a name="error-boundaries-ts"></a>
In UI frameworks like React, Error Boundaries are components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed. While not a direct TypeScript feature, designing your TypeScript components with this pattern in mind (if applicable to your framework) is good practice.

## 7. Structured Logging with Types <a name="structured-logging-ts"></a>
Define types for your log entries to ensure consistency and make logs easier to parse and analyze.
```typescript
interface LogEntry {
  timestamp: string;
  level: 'info' | 'warn' | 'error' | 'debug';
  message: string;
  context?: Record<string, any>; // Additional contextual data
  error?: {
    name: string;
    message: string;
    stack?: string;
    details?: any; // Custom error details
  };
}

function log(entry: LogEntry) {
  // In a real app, send this to a logging service or console
  console.log(JSON.stringify(entry));
}

// Example usage:
log({
  timestamp: new Date().toISOString(),
  level: 'error',
  message: 'Failed to process user payment',
  context: { userId: 'user123', orderId: 'order456' },
  error: {
    name: 'PaymentError',
    message: 'Insufficient funds',
    details: { attempt: 3 }
  }
});
```

## 8. Debugging TypeScript Code <a name="debugging-ts"></a>

### Source Maps <a name="source-maps-debugging-ts"></a>
Essential for debugging. They map your compiled JavaScript code back to your original TypeScript source, allowing you to debug directly in TypeScript.
In `tsconfig.json`:
```json
{
  "compilerOptions": {
    "sourceMap": true, // Generates .map files
    // "inlineSourceMap": true, // Alternative: embeds source maps in JS files (larger files)
    // "inlineSources": true, // Embeds original TS content in source maps (for easier debugging across systems)
  }
}
```

### IDE Debuggers (e.g., VS Code) <a name="ide-debuggers-ts"></a>
VS Code has excellent built-in debugging support for TypeScript.
*   **`launch.json`:** Configure how to launch your application for debugging (Node.js, browser, tests).
    ```json
    // .vscode/launch.json example for a Node.js app
    {
      "version": "0.2.0",
      "configurations": [
        {
          "type": "node",
          "request": "launch",
          "name": "Launch Program",
          "skipFiles": ["<node_internals>/**"],
          "program": "${workspaceFolder}/dist/main.js", // Path to compiled JS entry point
          "preLaunchTask": "tsc: build - tsconfig.json", // Assumes a build task in tasks.json
          "outFiles": ["${workspaceFolder}/dist/**/*.js"],
          "sourceMaps": true
        }
      ]
    }
    ```
*   **Features:** Set breakpoints, step through code, inspect variables (which show their TypeScript types), use the debug console, set conditional breakpoints, and logpoints (breakpoints that log a message and continue).

### Browser Developer Tools <a name="browser-dev-tools-ts"></a>
Modern browsers can use source maps to allow debugging TypeScript directly in the Sources panel.

### Leveraging the Type System During Debugging <a name="type-system-debugging-ts"></a>
*   When inspecting variables, pay attention to their inferred or actual runtime types versus what you expect from TypeScript's static analysis.
*   If a variable is `any` or `unknown`, it might be a sign of a type safety gap that needs addressing.

## 9. Static Analysis and Linters <a name="static-analysis-linters-ts"></a>
ESLint with TypeScript support (`@typescript-eslint`) can catch potential error-prone patterns and enforce coding standards related to error handling.
*   Example rules: `no-throw-literal`, `no-floating-promises`, `@typescript-eslint/no-explicit-any`.

## 10. Using `never` for Exhaustiveness in Error Handling <a name="never-exhaustiveness-ts"></a>
As shown in the discriminated union example, the `never` type can ensure that all cases in a `switch` statement or series of `if/else if` are handled. If a new error type is added to the union and not handled in the `switch`, TypeScript will raise a compile-time error at the `default` case where you assign to `never`.

## 11. Best Practices for Error Handling <a name="best-practices-error-handling-ts"></a>
*   **Fail Fast:** Catch errors as close to the source as possible.
*   **Be Specific:** Use custom error types or discriminated unions to provide context.
*   **Don't Suppress Errors:** Avoid empty `catch` blocks unless you intentionally and knowingly handle the error or re-throw a more specific one.
*   **Log Meaningfully:** Provide enough information in logs for debugging.
*   **Consistent Error Shapes:** Standardize how errors are structured, especially for APIs.
*   **User-Friendly Messages:** For user-facing errors, provide clear, actionable messages without exposing sensitive details.
*   **Clean Up Resources:** Use `finally` blocks or equivalent mechanisms (e.g., `addTeardown` in Angular, `useEffect` cleanup in React) to release resources even if errors occur.

## 12. Key Takeaways <a name="key-takeaways-ts-error-debug"></a>
*   Custom error classes and discriminated unions provide more expressive and type-safe error handling than generic `Error` objects.
*   Source maps are fundamental for an effective TypeScript debugging experience.
*   IDEs like VS Code offer powerful debugging tools that work seamlessly with TypeScript.
*   Structured logging with types improves the observability of your application.
*   Leverage TypeScript's type system and static analysis tools to prevent errors and aid in debugging.

By implementing these advanced error handling and debugging strategies, you can build more resilient, maintainable, and easier-to-diagnose TypeScript applications.

---

⬅️ [Back to Type-Safe Data Fetching and API Communication in TypeScript](../38-ts-api-communication/README.md) | ➡️ [Next: TypeScript and Functional Programming Concepts](../40-ts-functional-programming/README.md) *(Placeholder)*
