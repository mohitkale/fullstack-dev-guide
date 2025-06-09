# 15. Fetch API: Making Network Requests

The Fetch API provides a modern, powerful, and flexible interface for fetching resources across the network (e.g., from servers via HTTP). It's a Promise-based API, making it easier to handle asynchronous network operations compared to the older `XMLHttpRequest` (XHR) object.

## Table of Contents
1.  [What is the Fetch API?](#what-is-fetch-api)
    *   [Advantages over `XMLHttpRequest`](#fetch-advantages)
2.  [Basic Usage: `fetch()`](#basic-fetch)
    *   [Making a Simple GET Request](#simple-get-request)
    *   [The `Response` Object](#response-object)
        *   Properties (`ok`, `status`, `statusText`, `headers`, `url`, `type`)
        *   Methods to Read Body (`text()`, `json()`, `blob()`, `arrayBuffer()`, `formData()`)
3.  [Handling Responses](#handling-responses)
    *   [Checking for Successful Responses (`response.ok`)](#checking-response-ok)
    *   [Parsing Response Body](#parsing-response-body)
4.  [Making Different Types of Requests](#different-request-types)
    *   [The `Request` Object and `init` Options](#request-init-options)
    *   [POST Requests](#post-requests)
        *   [Sending JSON Data](#sending-json-data)
        *   [Sending Form Data (`FormData`)](#sending-formdata)
    *   [Other HTTP Methods (PUT, DELETE, etc.)](#other-http-methods)
5.  [Working with Headers](#working-with-headers)
    *   [The `Headers` Object](#headers-object)
    *   [Setting Request Headers](#setting-request-headers)
    *   [Reading Response Headers](#reading-response-headers)
6.  [Error Handling](#fetch-error-handling)
    *   [Network Errors vs. HTTP Errors](#network-vs-http-errors)
    *   [Using `try...catch` for Network Errors](#try-catch-network-errors)
    *   [Checking `response.ok` for HTTP Errors](#response-ok-http-errors)
7.  [CORS (Cross-Origin Resource Sharing)](#fetch-cors)
    *   [Simple Requests vs. Preflighted Requests](#simple-vs-preflight)
    *   [The `mode` Option (`cors`, `no-cors`, `same-origin`)](#fetch-mode-option)
8.  [Aborting a Fetch Request (`AbortController`)](#aborting-fetch)
9.  [Fetch API vs. `XMLHttpRequest`](#fetch-vs-xhr-detail)
10. [Key Takeaways](#key-takeaways-fetch)

## 1. What is the Fetch API? <a name="what-is-fetch-api"></a>
The Fetch API is a JavaScript interface for making HTTP requests to servers. It simplifies asynchronous data fetching and provides a more logical and flexible way to interact with network resources than `XMLHttpRequest`.

### Advantages over `XMLHttpRequest` <a name="fetch-advantages"></a>
*   **Promise-based**: Makes handling asynchronous operations cleaner with `then()`, `catch()`, and `async/await`.
*   **Simpler API**: More straightforward for common use cases.
*   **Flexible**: Better integration with other web technologies like Service Workers.
*   **Modern**: Designed to be the standard for network requests in browsers.

## 2. Basic Usage: `fetch()` <a name="basic-fetch"></a>
The global `fetch()` method starts the process of fetching a resource from the network. It takes one mandatory argument, the path to the resource you want to fetch (a URL string or a `Request` object).

### Making a Simple GET Request <a name="simple-get-request"></a>
By default, `fetch()` makes a GET request.
```javascript
fetch('https://api.example.com/data')
  .then(response => {
    // The fetch() promise resolves as soon as the server responds with headers,
    // not when the full response body is downloaded.
    console.log('Received response headers.');
    // We need to explicitly parse the body, e.g., as JSON
    if (!response.ok) { // Check if HTTP status is 200-299
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json(); // Returns another promise that resolves with the parsed JSON body
  })
  .then(data => {
    console.log('Data received:', data);
    // Process the data
  })
  .catch(error => {
    // This catch block handles network errors (e.g., DNS resolution failure, server unreachable)
    // and errors thrown from the .then() blocks (like the HTTP error check above).
    console.error('Fetch error:', error);
  });
```

### The `Response` Object <a name="response-object"></a>
The `fetch()` promise resolves to a `Response` object, which represents the response to a request. It doesn't contain the actual data (body) directly but provides methods to access it.

**Key `Response` Properties:**
*   `ok` (boolean): `true` if the HTTP status code is in the range 200-299 (successful), `false` otherwise.
*   `status` (number): The HTTP status code (e.g., `200`, `404`, `500`).
*   `statusText` (string): The status message corresponding to the status code (e.g., `"OK"`, `"Not Found"`).
*   `headers` (`Headers` object): An object allowing you to query the response headers.
*   `url` (string): The URL of the response.
*   `type` (string): The type of the response (e.g., `basic`, `cors`, `opaque`).
*   `body` (`ReadableStream`): The response body as a stream. You typically use one of the body-reading methods below instead of interacting with this directly.

**Methods to Read Response Body (these all return Promises):**
*   `response.text()`: Resolves with the response body as a string.
*   `response.json()`: Resolves with the response body parsed as JSON (JavaScript object/array).
*   `response.blob()`: Resolves with the response body as a `Blob` (binary data, e.g., for images).
*   `response.arrayBuffer()`: Resolves with the response body as an `ArrayBuffer` (raw binary data).
*   `response.formData()`: Resolves with the response body as `FormData` (for form submissions).

**Important**: The response body can only be read **once**. After calling one of these methods, the body stream is consumed.

## 3. Handling Responses <a name="handling-responses"></a>

### Checking for Successful Responses (`response.ok`) <a name="checking-response-ok"></a>
The `fetch()` promise resolves even if the HTTP response indicates an error (e.g., 404 Not Found, 500 Internal Server Error). It only rejects on network failures (e.g., no internet connection). Therefore, you must always check `response.ok` or `response.status`.

```javascript
fetch('https://api.example.com/nonexistent-endpoint')
  .then(response => {
    if (!response.ok) {
      // If response.ok is false, it means we got an HTTP error status (4xx or 5xx)
      console.error(`HTTP Error: ${response.status} ${response.statusText}`);
      // Optionally, throw an error to be caught by the .catch() block
      throw new Error(`Request failed with status ${response.status}`);
    }
    return response.json(); // Or response.text(), etc.
  })
  .then(data => {
    console.log('Data:', data);
  })
  .catch(error => {
    console.error('Fetch operation failed:', error);
  });
```

### Parsing Response Body <a name="parsing-response-body"></a>
Choose the appropriate method based on the expected content type.
```javascript
async function fetchDataAndParse() {
  try {
    const response = await fetch('https://api.example.com/users/1');
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    const userData = await response.json(); // Assuming the response is JSON
    console.log('User data:', userData);

    const textResponse = await fetch('https://api.example.com/readme.txt');
    if (!textResponse.ok) {
      throw new Error(`HTTP error! Status: ${textResponse.status}`);
    }
    const readmeText = await textResponse.text(); // Assuming plain text
    console.log('Readme content:', readmeText);

  } catch (error) {
    console.error('Failed to fetch or parse:', error);
  }
}
fetchDataAndParse();
```

## 4. Making Different Types of Requests <a name="different-request-types"></a>

### The `Request` Object and `init` Options <a name="request-init-options"></a>
`fetch()` can take an optional second argument, an `init` object, to customize the request. This object can include:
*   `method` (string): HTTP method (e.g., `"GET"`, `"POST"`, `"PUT"`, `"DELETE"`). Defaults to `"GET"`.
*   `headers` (object or `Headers` object): Request headers.
*   `body` (string, `Blob`, `BufferSource`, `FormData`, `URLSearchParams`, `ReadableStream`): The request body.
*   `mode` (string): Request mode (e.g., `"cors"`, `"no-cors"`, `"same-origin"`).
*   `credentials` (string): Cookie handling (e.g., `"include"`, `"same-origin"`, `"omit"`).
*   `cache` (string): Caching behavior.
*   `signal` (`AbortSignal`): For aborting the request.
*   And more...

Alternatively, you can create a `Request` object and pass it to `fetch()`:
```javascript
// const myRequest = new Request('https://api.example.com/submit', {
//   method: 'POST',
//   body: JSON.stringify({ message: 'Hello' })
// });
// fetch(myRequest).then(...);
```

### POST Requests <a name="post-requests"></a>

#### Sending JSON Data <a name="sending-json-data"></a>
When sending JSON, you need to:
1.  Stringify the JavaScript object using `JSON.stringify()`.
2.  Set the `Content-Type` header to `application/json`.

```javascript
const postData = { name: 'New Item', value: 100 };

fetch('https://api.example.com/items', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    // 'Authorization': 'Bearer YOUR_TOKEN_HERE' // Example for auth
  },
  body: JSON.stringify(postData) // Convert JS object to JSON string
})
.then(response => {
  if (!response.ok) {
    throw new Error(`HTTP error! Status: ${response.status}`);
  }
  return response.json(); // Or response.text() if server sends non-JSON response
})
.then(data => {
  console.log('POST success:', data);
})
.catch(error => {
  console.error('POST error:', error);
});
```

#### Sending Form Data (`FormData`) <a name="sending-formdata"></a>
`FormData` is useful for sending data as if it were submitted from an HTML form (e.g., for file uploads or `multipart/form-data` requests).
When using `FormData`, the browser automatically sets the `Content-Type` header to `multipart/form-data` with the correct boundary.

```javascript
const formData = new FormData();
formData.append('username', 'testuser');
formData.append('email', 'test@example.com');
// To append a file from an <input type="file"> element:
// const fileInput = document.querySelector('input[type="file"]');
// formData.append('avatar', fileInput.files[0]);

fetch('https://api.example.com/users', {
  method: 'POST',
  body: formData // No need to set Content-Type header manually for FormData
})
.then(response => response.json())
.then(result => console.log('FormData POST success:', result))
.catch(error => console.error('FormData POST error:', error));
```

### Other HTTP Methods (PUT, DELETE, etc.) <a name="other-http-methods"></a>
Other methods are specified similarly:
```javascript
// PUT request (e.g., to update an existing resource)
fetch('https://api.example.com/items/123', {
  method: 'PUT',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'Updated Item', value: 150 })
})
.then(response => response.json())
.then(data => console.log('PUT success:', data));

// DELETE request
fetch('https://api.example.com/items/123', {
  method: 'DELETE'
})
.then(response => {
  if (response.ok) {
    console.log('DELETE success, status:', response.status); // e.g., 200 OK or 204 No Content
  } else {
    throw new Error(`DELETE failed with status ${response.status}`);
  }
});
```

## 5. Working with Headers <a name="working-with-headers"></a>

### The `Headers` Object <a name="headers-object"></a>
Both request and response headers are managed using the `Headers` object.

```javascript
const myHeaders = new Headers();
myHeaders.append('Content-Type', 'application/json');
myHeaders.append('X-Custom-Header', 'MyValue');

// Check if a header exists
console.log(myHeaders.has('Content-Type')); // true

// Get a header value
console.log(myHeaders.get('X-Custom-Header')); // MyValue

// Iterate over headers
// for (const [key, value] of myHeaders.entries()) {
//   console.log(`${key}: ${value}`);
// }
```

### Setting Request Headers <a name="setting-request-headers"></a>
You can pass a `Headers` object or a plain JavaScript object to the `headers` option in `fetch()`.
```javascript
fetch('https://api.example.com/data', {
  headers: {
    'Accept': 'application/json',
    'Authorization': 'Bearer your_token_here'
  }
});
// or using Headers object
// const reqHeaders = new Headers({
//   'Accept': 'application/json',
//   'Authorization': 'Bearer your_token_here'
// });
// fetch('https://api.example.com/data', { headers: reqHeaders });
```

### Reading Response Headers <a name="reading-response-headers"></a>
The `response.headers` property is a `Headers` object.
```javascript
fetch('https://api.example.com/data')
  .then(response => {
    console.log('Content-Type from response:', response.headers.get('Content-Type'));
    console.log('Date from response:', response.headers.get('Date'));

    // Iterate over all response headers
    // for (let [key, value] of response.headers) {
    //   console.log(`${key} = ${value}`);
    // }
    return response.json();
  })
  .then(data => console.log(data));
```

## 6. Error Handling <a name="fetch-error-handling"></a>

### Network Errors vs. HTTP Errors <a name="network-vs-http-errors"></a>
*   **Network Errors**: The `fetch()` promise **rejects** if a network error occurs (e.g., DNS failure, server unreachable, CORS issues preventing the request). These are caught by the `.catch()` block or a `try...catch` with `async/await`.
*   **HTTP Errors**: The `fetch()` promise **resolves** successfully even if the server responds with an HTTP error status (like 404 Not Found or 500 Internal Server Error). You must check `response.ok` or `response.status` to handle these.

### Using `try...catch` for Network Errors (with `async/await`) <a name="try-catch-network-errors"></a>
```javascript
async function fetchDataAsync() {
  try {
    const response = await fetch('https://api.example.com/data'); // Network error might occur here
    // ... rest of the logic
  } catch (networkError) {
    // This catches network errors (e.g., server down, no internet)
    console.error('Network error:', networkError.message);
  }
}
```

### Checking `response.ok` for HTTP Errors <a name="response-ok-http-errors"></a>
```javascript
async function fetchDataAndHandleHTTPErrors() {
  try {
    const response = await fetch('https://api.example.com/possibly-missing-resource');
    
    if (!response.ok) {
      // Handle HTTP errors (4xx, 5xx)
      const errorData = await response.text(); // Or .json() if server sends error details in JSON
      throw new Error(`HTTP error ${response.status}: ${response.statusText}. Server says: ${errorData}`);
    }
    
    const data = await response.json();
    console.log('Success:', data);

  } catch (error) {
    // Catches both network errors and explicitly thrown HTTP errors
    console.error('Fetch operation failed:', error.message);
  }
}
fetchDataAndHandleHTTPErrors();
```

## 7. CORS (Cross-Origin Resource Sharing) <a name="fetch-cors"></a>
When making requests to a different origin (domain, protocol, or port), browsers enforce CORS policies. The server must include appropriate CORS headers (like `Access-Control-Allow-Origin`) for the request to succeed.

### Simple Requests vs. Preflighted Requests <a name="simple-vs-preflight"></a>
*   **Simple Requests**: Certain requests (e.g., GET, HEAD, POST with specific `Content-Type`s) don't require a preflight `OPTIONS` request.
*   **Preflighted Requests**: Requests with custom headers, methods other than GET/HEAD/POST, or certain `Content-Type`s trigger a preflight `OPTIONS` request by the browser to check server permissions before sending the actual request.

Fetch handles this automatically, but if the server isn't configured for CORS, the request will fail (often as a network error in the `catch` block).

### The `mode` Option (`cors`, `no-cors`, `same-origin`) <a name="fetch-mode-option"></a>
*   `mode: 'cors'` (default): Standard CORS request. The response must have CORS headers.
*   `mode: 'no-cors'`: Allows requests to other origins that don't have CORS headers. However, the response body will be opaque (inaccessible), and you can't read most headers. Useful for things like service workers caching resources or firing off a request without caring about the response.
*   `mode: 'same-origin'`: Only allows requests to the same origin. If the URL is for a different origin, the request will fail.

## 8. Aborting a Fetch Request (`AbortController`) <a name="aborting-fetch"></a>
You can abort a `fetch()` request using an `AbortController`.

```javascript
const controller = new AbortController();
const signal = controller.signal;

// Set a timeout to abort the request after 5 seconds
const timeoutId = setTimeout(() => controller.abort(), 5000);

fetch('https://api.example.com/long-running-data', { signal })
  .then(response => {
    clearTimeout(timeoutId); // Clear the timeout if fetch completes/errors before timeout
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }
    return response.json();
  })
  .then(data => {
    console.log('Data received:', data);
  })
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch aborted by user or timeout.');
    } else {
      console.error('Fetch error:', error);
    }
    clearTimeout(timeoutId); // Also clear timeout on other errors
  });

// To abort manually (e.g., on button click):
// controller.abort();
```
When aborted, the `fetch()` promise rejects with an `AbortError`.

## 9. Fetch API vs. `XMLHttpRequest` (Detailed) <a name="fetch-vs-xhr-detail"></a>
| Feature                  | Fetch API                                      | `XMLHttpRequest` (XHR)                          |
|--------------------------|------------------------------------------------|-------------------------------------------------|
| **API Style**            | Promise-based, modern                          | Event-based, older                              |
| **Asynchronous Handling**| `then`/`catch`/`finally`, `async`/`await`      | Event handlers (`onload`, `onerror`, `onprogress` etc.)|
| **Response Body**        | Stream-based, methods like `json()`, `text()`  | `responseText`, `responseXML` properties        |
| **Error Handling**       | Rejects on network errors only; check `response.ok` for HTTP errors | `onerror` for network errors; check `status` for HTTP errors |
| **Configuration**        | `init` object for options                      | Setting properties on XHR instance              |
| **CORS**                 | Built-in, controlled by `mode` option          | Handled by browser, less direct control         |
| **Request/Response Objects**| `Request` and `Response` objects               | Single XHR object manages both                  |
| **Streaming**            | Supports request and response streaming        | Limited streaming capabilities                  |
| **Abort**                | `AbortController`                              | `abort()` method on XHR instance                |
| **Cookie Handling**      | `credentials` option (`include`, `omit`, `same-origin`) | `withCredentials` property (boolean)            |

Fetch is generally preferred for new development due to its cleaner API and better integration with modern JavaScript features.

## 10. Key Takeaways <a name="key-takeaways-fetch"></a>
*   The Fetch API provides a modern, Promise-based interface for network requests.
*   `fetch(url, options)` returns a Promise that resolves to a `Response` object.
*   The `Response` object doesn't contain the body directly; use methods like `response.json()`, `response.text()` (which return Promises) to access it.
*   The `fetch()` promise only rejects on network errors. You **must** check `response.ok` or `response.status` for HTTP errors (e.g., 404, 500).
*   Use the `init` object (second argument to `fetch`) to configure method, headers, body, etc.
*   `Content-Type` header is crucial for POST/PUT requests (e.g., `application/json`).
*   CORS policies apply to cross-origin requests.
*   `AbortController` can be used to cancel fetch requests.

---

⬅️ [Back to Web Storage API](../14-js-web-storage/README.md) | ➡️ [Next Section: JavaScript Regular Expressions](../16-js-regex/README.md)
