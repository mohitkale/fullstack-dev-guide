# 10. Asynchronous JavaScript: Callbacks, Promises, and Async/Await

JavaScript is a single-threaded language, meaning it can only execute one piece of code at a time. However, many operations, like fetching data from a server, reading files, or waiting for user input, can take time. If JavaScript waited for these operations to complete before doing anything else, the user interface would freeze, leading to a poor user experience. Asynchronous programming allows JavaScript to perform these long-running tasks without blocking the main thread.

## Table of Contents
1.  [What is Asynchronous Programming?](#what-is-async)
    *   [Synchronous vs. Asynchronous](#sync-vs-async)
    *   [Why Asynchronous JavaScript?](#why-async-js)
2.  [The Event Loop](#event-loop)
    *   [Call Stack, Web APIs / C++ APIs (Node.js), Callback Queue (Task Queue), Microtask Queue](#event-loop-components)
3.  [Callbacks](#callbacks)
    *   [Basic Callbacks](#basic-callbacks)
    *   [Callback Hell (Pyramid of Doom)](#callback-hell)
4.  [Promises](#promises)
    *   [What is a Promise?](#what-is-promise)
    *   [States of a Promise (Pending, Fulfilled, Rejected)](#promise-states)
    *   [Creating a Promise (`new Promise()`)](#creating-promise)
    *   [Consuming Promises: `.then()`, `.catch()`, `.finally()`](#consuming-promises)
    *   [Chaining Promises](#chaining-promises)
    *   [Error Handling in Promises](#promise-error-handling)
    *   [Static Promise Methods](#static-promise-methods)
        *   [`Promise.all()`](#promise-all)
        *   [`Promise.race()`](#promise-race)
        *   [`Promise.allSettled()`](#promise-allsettled)
        *   [`Promise.any()`](#promise-any)
        *   [`Promise.resolve()` and `Promise.reject()`](#promise-resolve-reject)
5.  [Async/Await (ES2017)](#async-await)
    *   [`async` Functions](#async-functions)
    *   [`await` Keyword](#await-keyword)
    *   [Error Handling with `try...catch`](#async-await-error-handling)
    *   [Benefits of Async/Await](#async-await-benefits)
    *   [Async/Await with `Promise.all()`](#async-await-promise-all)
6.  [Choosing Between Callbacks, Promises, and Async/Await](#choosing-async-pattern)
7.  [Key Takeaways](#key-takeaways-async)

## 1. What is Asynchronous Programming? <a name="what-is-async"></a>
Asynchronous programming is a paradigm that allows tasks to run independently of the main program flow. Instead of waiting for a task to finish, the program can continue executing other code. When the asynchronous task completes, it notifies the program (often via a callback or by resolving a promise).

### Synchronous vs. Asynchronous <a name="sync-vs-async"></a>
*   **Synchronous (Sync)**: Tasks are executed one after another. Each task must complete before the next one begins. If a task takes a long time, it blocks further execution.
    ```javascript
    console.log("First task");
    // Simulate a long task (blocking)
    // for (let i = 0; i < 1e9; i++) {}
    console.log("Second task (waited for first)");
    ```
*   **Asynchronous (Async)**: Tasks can be initiated, and the program can move on to other tasks without waiting for the initiated task to complete. The initiated task runs in the background and signals completion later.
    ```javascript
    console.log("First task");
    setTimeout(() => {
      console.log("Async task completed (after 1 second)");
    }, 1000);
    console.log("Second task (didn't wait for async task)");
    // Output order:
    // First task
    // Second task (didn't wait for async task)
    // Async task completed (after 1 second)
    ```

### Why Asynchronous JavaScript? <a name="why-async-js"></a>
JavaScript is commonly used in environments where responsiveness is key:
*   **Browsers**: Keeping the UI responsive to user interactions (clicks, scrolls) while fetching data or running animations.
*   **Node.js Servers**: Handling multiple client requests concurrently without one slow request blocking others.

## 2. The Event Loop <a name="event-loop"></a>
JavaScript's concurrency model is based on an **event loop**. It's what allows JavaScript, despite being single-threaded, to perform non-blocking asynchronous operations.

### Key Components <a name="event-loop-components"></a>
1.  **Call Stack**: A LIFO (Last-In, First-Out) stack that keeps track of function calls. When a function is called, it's added to the stack. When it returns, it's removed.
2.  **Web APIs (Browsers) / C++ APIs (Node.js)**: These are environments outside the JavaScript engine that handle asynchronous operations (e.g., `setTimeout`, DOM events, `fetch` API calls, file system operations in Node.js). When an async operation is initiated, it's handed off to these APIs.
3.  **Callback Queue (Task Queue)**: A FIFO (First-In, First-Out) queue where messages (callbacks from completed async operations) are placed. For example, when `setTimeout` finishes or a `fetch` request completes, its callback function is added to this queue.
4.  **Microtask Queue**: A separate queue for microtasks, primarily promises' `.then()`, `.catch()`, `.finally()` callbacks, and `queueMicrotask()`. Microtasks have higher priority than regular tasks in the callback queue. The event loop processes all microtasks after each task from the callback queue or after the call stack becomes empty.

**How it works:**
*   The event loop continuously checks if the call stack is empty.
*   If the call stack is empty, it takes the first task from the microtask queue (if any) and pushes it onto the call stack for execution.
*   If the microtask queue is empty, it takes the first task from the callback queue (if any) and pushes it onto the call stack.
*   This process repeats, allowing JavaScript to handle asynchronous events without blocking.

```javascript
console.log('Start'); // 1. Call stack

setTimeout(() => { // 4. Callback to Task Queue after 0ms
  console.log('Timeout callback'); // 7. Call stack (after microtasks)
}, 0);

Promise.resolve().then(() => { // 5. Callback to Microtask Queue
  console.log('Promise resolved (microtask)'); // 6. Call stack (before timeout)
});

console.log('End'); // 2. Call stack

// Output Order:
// Start
// End
// Promise resolved (microtask)
// Timeout callback
```

## 3. Callbacks <a name="callbacks"></a>
A callback is a function passed as an argument to another function, which is then invoked inside the outer function to complete some kind of routine or action.

### Basic Callbacks <a name="basic-callbacks"></a>
```javascript
function fetchData(url, callback) {
  // Simulate fetching data
  console.log(`Fetching data from ${url}...`);
  setTimeout(() => {
    const data = { message: "Data received!" };
    callback(null, data); // Convention: first arg is error, second is data
  }, 1500);
}

function handleData(error, data) {
  if (error) {
    console.error("Error:", error);
    return;
  }
  console.log("Success:", data.message);
}

fetchData("https://api.example.com/data", handleData);
console.log("Request initiated...");
```

### Callback Hell (Pyramid of Doom) <a name="callback-hell"></a>
When dealing with multiple dependent asynchronous operations using callbacks, you can end up with deeply nested functions, making the code hard to read and maintain. This is known as "callback hell" or the "pyramid of doom."

```javascript
// Example of Callback Hell
/*
asyncOperation1(data1, function(error1, result1) {
  if (error1) { handleError(error1); return; }
  asyncOperation2(result1, function(error2, result2) {
    if (error2) { handleError(error2); return; }
    asyncOperation3(result2, function(error3, result3) {
      if (error3) { handleError(error3); return; }
      // ... and so on
      console.log("All operations complete:", result3);
    });
  });
});
*/
```
Promises and async/await were introduced to solve this problem.

## 4. Promises <a name="promises"></a>
A Promise is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value.

### What is a Promise? <a name="what-is-promise"></a>
A promise is a placeholder for a value that will be available later. It allows you to associate handlers with an asynchronous action's eventual success value or failure reason.

### States of a Promise <a name="promise-states"></a>
A Promise can be in one of three states:
1.  **Pending**: Initial state, neither fulfilled nor rejected.
2.  **Fulfilled (Resolved)**: The operation completed successfully, and the promise has a resulting value.
3.  **Rejected**: The operation failed, and the promise has a reason for the failure.

Once a promise is fulfilled or rejected, it is settled and its state cannot change.

### Creating a Promise (`new Promise()`) <a name="creating-promise"></a>
You create a promise using the `Promise` constructor, which takes an executor function as an argument. The executor function itself takes two arguments: `resolve` and `reject`.

```javascript
function fetchDataWithPromise(url) {
  return new Promise((resolve, reject) => {
    console.log(`Fetching data from ${url} with Promise...`);
    setTimeout(() => {
      const success = Math.random() > 0.3; // Simulate success/failure
      if (success) {
        const data = { message: "Data received via Promise!" };
        resolve(data); // Fulfill the promise with data
      } else {
        reject(new Error("Failed to fetch data.")); // Reject the promise with an error
      }
    }, 1500);
  });
}
```

### Consuming Promises: `.then()`, `.catch()`, `.finally()` <a name="consuming-promises"></a>
*   **`.then(onFulfilled, onRejected)`**: Attaches callbacks for the resolution and/or rejection of the Promise.
    *   `onFulfilled`: Called if the promise is fulfilled. Receives the fulfillment value.
    *   `onRejected`: Called if the promise is rejected. Receives the rejection reason. (Often, `.catch()` is preferred for error handling).
*   **`.catch(onRejected)`**: Attaches a callback only for the rejection of the Promise. Syntactic sugar for `.then(null, onRejected)`.
*   **`.finally(onFinally)`**: Attaches a callback that is executed when the promise is settled (either fulfilled or rejected). Useful for cleanup operations.

```javascript
const myPromise = fetchDataWithPromise("https://api.example.com/promise-data");

myPromise
  .then((data) => {
    console.log("Promise Fulfilled:", data.message);
    return data.message.toUpperCase(); // Value returned here is passed to the next .then()
  })
  .then((uppercasedMessage) => {
    console.log("Transformed Data:", uppercasedMessage);
  })
  .catch((error) => {
    console.error("Promise Rejected:", error.message);
  })
  .finally(() => {
    console.log("Promise operation finished (settled).");
  });
```

### Chaining Promises <a name="chaining-promises"></a>
`.then()` and `.catch()` return a new promise, allowing you to chain asynchronous operations in a more readable way than nested callbacks.
If a `.then()` callback returns a value, the next `.then()` in the chain receives that value.
If a `.then()` callback returns a new promise, the next `.then()` waits for that new promise to settle.

### Error Handling in Promises <a name="promise-error-handling"></a>
A single `.catch()` at the end of a promise chain can handle errors from any preceding promise in the chain, unless an earlier `.catch()` handles it and doesn't re-throw.

### Static Promise Methods <a name="static-promise-methods"></a>

#### `Promise.all(iterable)` <a name="promise-all"></a>
Takes an iterable of promises and returns a single `Promise` that fulfills when *all* of the input promises have fulfilled. It rejects if *any* of the input promises reject.
The fulfillment value is an array of the fulfillment values from the input promises, in the same order.
```javascript
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise((resolve) => setTimeout(resolve, 100, 'foo'));

Promise.all([promise1, promise2, promise3])
  .then((values) => console.log("Promise.all fulfilled:", values)); // [3, 42, "foo"]
  // .catch(error => console.error("Promise.all rejected:", error));
```

#### `Promise.race(iterable)` <a name="promise-race"></a>
Takes an iterable of promises and returns a single `Promise` that settles (fulfills or rejects) as soon as *one* of the input promises settles. The resulting promise takes on the state and value of the first promise to settle.
```javascript
const p1 = new Promise((resolve) => setTimeout(resolve, 500, 'one'));
const p2 = new Promise((resolve, reject) => setTimeout(reject, 100, new Error('two failed first')));

Promise.race([p1, p2])
  .then((value) => console.log("Promise.race fulfilled:", value))
  .catch((error) => console.error("Promise.race rejected:", error.message)); // "two failed first"
```

#### `Promise.allSettled(iterable)` <a name="promise-allsettled"></a>
Takes an iterable of promises and returns a single `Promise` that fulfills when *all* input promises have settled (either fulfilled or rejected). The fulfillment value is an array of objects, each describing the outcome of a promise (`{status: 'fulfilled', value: ...}` or `{status: 'rejected', reason: ...}`).
Useful when you want to know the outcome of all promises, regardless of individual failures.
```javascript
const pAllSet1 = Promise.resolve("Success!");
const pAllSet2 = Promise.reject(new Error("Failure!"));

Promise.allSettled([pAllSet1, pAllSet2])
  .then(results => results.forEach(result => console.log(result.status, result.value || result.reason)));
// fulfilled Success!
// rejected Error: Failure!
```

#### `Promise.any(iterable)` <a name="promise-any"></a>
Takes an iterable of promises and returns a single `Promise` that fulfills as soon as *one* of the input promises fulfills. If all input promises reject, it rejects with an `AggregateError` containing all rejection reasons.
```javascript
const pAny1 = Promise.reject("Fail 1");
const pAny2 = new Promise(resolve => setTimeout(resolve, 100, "Success 2"));
const pAny3 = new Promise(resolve => setTimeout(resolve, 50, "Success 3 (faster)"));

Promise.any([pAny1, pAny2, pAny3])
  .then(value => console.log("Promise.any fulfilled:", value)) // "Success 3 (faster)"
  .catch(error => console.error("Promise.any rejected:", error));
```

#### `Promise.resolve(value)` and `Promise.reject(reason)` <a name="promise-resolve-reject"></a>
*   `Promise.resolve(value)`: Returns a `Promise` object that is resolved with the given value. If the value is a promise, that promise is returned; if the value is a thenable (i.e. has a "then" method), `Promise.resolve()` will follow that thenable, adopting its eventual state.
*   `Promise.reject(reason)`: Returns a `Promise` object that is rejected with the given reason.

## 5. Async/Await (ES2017) <a name="async-await"></a>
`async/await` is syntactic sugar built on top of Promises, making asynchronous code look and behave a bit more like synchronous code, which can improve readability.

### `async` Functions <a name="async-functions"></a>
The `async` keyword is used to declare an asynchronous function. An `async` function always returns a `Promise`. If the function returns a value, the `Promise` will be resolved with that value. If the function throws an error, the `Promise` will be rejected with that error.
```javascript
async function myAsyncFunction() {
  return "Hello from async function!"; // This will be wrapped in a resolved Promise
}

myAsyncFunction().then(console.log); // Hello from async function!

async function myAsyncErrorFunction() {
  throw new Error("Error in async function!");
}

myAsyncErrorFunction().catch(err => console.error(err.message)); // Error in async function!
```

### `await` Keyword <a name="await-keyword"></a>
The `await` keyword can only be used inside an `async` function. It pauses the execution of the `async` function until the `Promise` it's waiting for is settled (resolved or rejected).
*   If the `Promise` resolves, `await` returns the resolved value.
*   If the `Promise` rejects, `await` throws the rejected error (which can be caught with `try...catch`).

```javascript
function delayedResolve(value, delay) {
  return new Promise(resolve => setTimeout(() => resolve(value), delay));
}

async function process() {
  console.log("Starting process...");
  const result1 = await delayedResolve("First result", 1000);
  console.log(result1);

  const result2 = await delayedResolve("Second result", 500);
  console.log(result2);

  console.log("Process finished.");
  return "All done!";
}

process().then(finalMsg => console.log(finalMsg));
```

### Error Handling with `try...catch` <a name="async-await-error-handling"></a>
You can use standard `try...catch` blocks for error handling with `await`, just like synchronous code.
```javascript
async function fetchDataWithErrorHandling() {
  try {
    console.log("Attempting to fetch data...");
    const response = await fetch("https://nonexistent.url/data"); // This will likely fail
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    const data = await response.json();
    console.log("Data fetched:", data);
    return data;
  } catch (error) {
    console.error("fetchDataWithErrorHandling failed:", error.message);
    // return null; // Or re-throw, or handle appropriately
  }
}

fetchDataWithErrorHandling();
```

### Benefits of Async/Await <a name="async-await-benefits"></a>
*   **Readability**: Makes asynchronous code look more synchronous and easier to follow.
*   **Error Handling**: Simplifies error handling with `try...catch`.
*   **Debugging**: Easier to debug as the call stack is more intuitive.

### Async/Await with `Promise.all()` <a name="async-await-promise-all"></a>
You can use `await` with `Promise.all()` to wait for multiple promises to resolve concurrently.
```javascript
async function fetchMultipleData() {
  try {
    const [data1, data2, data3] = await Promise.all([
      fetch('https://api.example.com/data1').then(res => res.json()),
      fetch('https://api.example.com/data2').then(res => res.json()),
      fetch('https://api.example.com/data3').then(res => res.json())
    ]);
    console.log("All data fetched:", data1, data2, data3);
  } catch (error) {
    console.error("Error fetching multiple data:", error);
  }
}
```

## 6. Choosing Between Callbacks, Promises, and Async/Await <a name="choosing-async-pattern"></a>
*   **Callbacks**: Still used in some older APIs and simple scenarios. Avoid callback hell.
*   **Promises**: A significant improvement over callbacks for managing asynchronous operations. Good for complex chains and error handling.
*   **Async/Await**: Generally the preferred way for writing asynchronous JavaScript today due to its readability and simpler error handling. It's built on Promises, so understanding Promises is still essential.

Many modern JavaScript libraries and APIs are promise-based, making `async/await` a natural fit.

## 7. Key Takeaways <a name="key-takeaways-async"></a>
*   Asynchronous programming is essential for non-blocking operations in JavaScript.
*   The **Event Loop** (with Call Stack, Web/C++ APIs, Callback Queue, Microtask Queue) enables JavaScript's concurrency.
*   **Callbacks** are functions passed to be executed later, but can lead to "callback hell."
*   **Promises** represent the eventual result of an async operation, offering better control and readability with `.then()`, `.catch()`, and `.finally()`.
*   **`async/await`** provides syntactic sugar over Promises, making async code look more synchronous and easier to manage with `try...catch`.
*   Understanding these concepts is crucial for building responsive and efficient JavaScript applications.

---

⬅️ [Back to Understanding `this` in JavaScript](../09-js-this-keyword/README.md) | ➡️ [Next Section: JavaScript Modules](../11-js-modules/README.md)
