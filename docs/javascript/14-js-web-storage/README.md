# 14. Web Storage API: `localStorage` and `sessionStorage`

The Web Storage API provides mechanisms for web applications to store data locally within the user's browser. This allows for persisting user preferences, caching data, or maintaining application state across sessions or within a single session. The two main storage mechanisms provided by this API are `localStorage` and `sessionStorage`.

## Table of Contents
1.  [What is Web Storage?](#what-is-web-storage)
2.  [`localStorage`](#localstorage)
    *   [Characteristics](#localstorage-characteristics)
    *   [Use Cases](#localstorage-use-cases)
3.  [`sessionStorage`](#sessionstorage)
    *   [Characteristics](#sessionstorage-characteristics)
    *   [Use Cases](#sessionstorage-use-cases)
4.  [Key Differences: `localStorage` vs. `sessionStorage`](#local-vs-session)
5.  [Web Storage API Methods](#web-storage-api-methods)
    *   [`setItem(key, value)`](#setitem)
    *   [`getItem(key)`](#getitem)
    *   [`removeItem(key)`](#removeitem)
    *   [`clear()`](#clear)
    *   [`key(index)`](#key-method)
    *   [`length`](#length-property)
6.  [Working with Data (Storing Non-String Values)](#storing-non-string-data)
    *   [Using `JSON.stringify()` and `JSON.parse()`](#json-for-storage)
7.  [Storage Limits and Considerations](#storage-limits)
    *   [Storage Capacity](#storage-capacity)
    *   [Synchronous Operations](#synchronous-operations)
    *   [Security (Same-Origin Policy)](#storage-security)
    *   [Data Sensitivity](#data-sensitivity)
8.  [The `storage` Event](#storage-event)
9.  [Web Storage vs. Cookies](#storage-vs-cookies)
10. [Key Takeaways](#key-takeaways-web-storage)

## 1. What is Web Storage? <a name="what-is-web-storage"></a>
Web Storage, sometimes known as DOM Storage, is a way for web pages to store key/value pairs locally within the user's web browser. Unlike cookies, this data is not automatically sent with every HTTP request, which can save bandwidth. It provides larger storage capacity than cookies and offers two distinct storage objects: `localStorage` and `sessionStorage`.

Both `localStorage` and `sessionStorage` are available as properties on the global `window` object.

## 2. `localStorage` <a name="localstorage"></a>
The `localStorage` object stores data with no expiration date. The data will not be deleted when the browser is closed and will be available the next day, week, or year, unless explicitly cleared by the user (e.g., by clearing browser data) or by web application code.

### Characteristics <a name="localstorage-characteristics"></a>
*   **Persistence**: Data persists until explicitly deleted. Closing the browser or tab does not affect it.
*   **Scope**: Data is scoped to the document's origin (protocol, hostname, and port). All pages from the same origin can access the same `localStorage` data.
*   **Capacity**: Typically around 5-10MB per origin (varies by browser).
*   **Accessibility**: Available via `window.localStorage`.

### Use Cases <a name="localstorage-use-cases"></a>
*   Storing user preferences (e.g., theme settings, language choice).
*   Caching application data to reduce server requests and improve performance.
*   Keeping a user logged in across browser sessions (though more secure methods like HTTP-only cookies are often preferred for sensitive tokens).
*   Saving application state that needs to persist even if the user closes the browser.

```javascript
// Setting an item in localStorage
localStorage.setItem('username', 'JohnDoe');
localStorage.setItem('theme', 'dark');

// Getting an item from localStorage
const username = localStorage.getItem('username');
console.log(username); // Output: JohnDoe

// Removing an item
// localStorage.removeItem('theme');

// Clearing all items for the origin
// localStorage.clear();
```

## 3. `sessionStorage` <a name="sessionstorage"></a>
The `sessionStorage` object stores data for only one session. The data is deleted when the browser tab is closed. If the user opens multiple tabs or windows from the same origin, each tab/window will have its own separate `sessionStorage`.

### Characteristics <a name="sessionstorage-characteristics"></a>
*   **Persistence**: Data persists only for the duration of the page session (i.e., as long as the browser tab is open). Closing the tab or browser clears the data.
*   **Scope**: Data is scoped to the document's origin *and* the current browser tab or window. Data in one tab is not accessible to another tab, even if they share the same origin.
*   **Capacity**: Similar to `localStorage`, typically around 5-10MB per origin (varies by browser).
*   **Accessibility**: Available via `window.sessionStorage`.

### Use Cases <a name="sessionstorage-use-cases"></a>
*   Storing temporary data for a single user session, like user input in a multi-step form.
*   Keeping track of the state of a single-page application (SPA) during a user's current visit.
*   Storing shopping cart contents before checkout if persistence across browser closures isn't required.

```javascript
// Setting an item in sessionStorage
sessionStorage.setItem('currentStep', '3');
sessionStorage.setItem('formData', JSON.stringify({ name: 'Test User', email: 'test@example.com' }));

// Getting an item from sessionStorage
const currentStep = sessionStorage.getItem('currentStep');
console.log(currentStep); // Output: 3

// Data will be cleared when the tab is closed.
```

## 4. Key Differences: `localStorage` vs. `sessionStorage` <a name="local-vs-session"></a>
| Feature         | `localStorage`                                  | `sessionStorage`                                     |
|-----------------|-------------------------------------------------|------------------------------------------------------|
| **Persistence** | Persists until explicitly deleted.                | Persists only for the current browser tab/session.   |
| **Scope**       | Shared across all tabs/windows from same origin. | Specific to each tab/window from the same origin.    |
| **Expiration**  | No expiration.                                  | Cleared when tab/browser is closed.                  |
| **Accessibility**| `window.localStorage`                           | `window.sessionStorage`                              |

## 5. Web Storage API Methods <a name="web-storage-api-methods"></a>
Both `localStorage` and `sessionStorage` implement the `Storage` interface, so they share the same methods and properties:

### `setItem(key, value)` <a name="setitem"></a>
Adds a key/value pair to storage. If the key already exists, its value is updated.
*   `key`: A string representing the name of the key.
*   `value`: A string representing the value to store. **Both key and value must be strings.**
```javascript
localStorage.setItem('userId', '12345');
sessionStorage.setItem('sessionToken', 'abcxyz789');
```

### `getItem(key)` <a name="getitem"></a>
Retrieves the value associated with the given key. If the key does not exist, it returns `null`.
```javascript
const userId = localStorage.getItem('userId'); // "12345"
const nonExistent = localStorage.getItem('nonExistentKey'); // null
```

### `removeItem(key)` <a name="removeitem"></a>
Removes the key/value pair associated with the given key from storage.
```javascript
localStorage.removeItem('userId');
```

### `clear()` <a name="clear"></a>
Removes all key/value pairs from storage for the current origin (for `localStorage`) or current session (for `sessionStorage`).
```javascript
// localStorage.clear(); // Clears all localStorage for this origin
// sessionStorage.clear(); // Clears all sessionStorage for this tab
```

### `key(index)` <a name="key-method"></a>
Returns the name of the key at the given numerical `index` in the storage. The order of keys is user-agent defined, so it's not reliable for iterating in a specific order.
```javascript
localStorage.setItem('a', '1');
localStorage.setItem('b', '2');
const firstKey = localStorage.key(0); // Might be 'a' or 'b'
console.log(firstKey);
```

### `length` <a name="length-property"></a>
A read-only property that returns the number of key/value pairs currently stored.
```javascript
localStorage.setItem('user', 'Alice');
localStorage.setItem('mode', 'light');
console.log(localStorage.length); // Output: 2
```

## 6. Working with Data (Storing Non-String Values) <a name="storing-non-string-data"></a>
Web Storage can only store strings. If you need to store objects, arrays, numbers, or booleans, you must convert them to strings before storing and parse them back when retrieving.

### Using `JSON.stringify()` and `JSON.parse()` <a name="json-for-storage"></a>
`JSON.stringify()` converts a JavaScript object or array into a JSON string.
`JSON.parse()` converts a JSON string back into a JavaScript object or array.

```javascript
const userProfile = {
  username: 'TechGuru',
  preferences: { theme: 'dark', notifications: true },
  lastLogin: new Date().toISOString()
};

// Storing an object
localStorage.setItem('userProfile', JSON.stringify(userProfile));

// Retrieving and parsing the object
const storedProfileString = localStorage.getItem('userProfile');
if (storedProfileString) {
  const retrievedProfile = JSON.parse(storedProfileString);
  console.log(retrievedProfile.username); // Output: TechGuru
  console.log(retrievedProfile.preferences.theme); // Output: dark
  // Note: Dates will be strings, need further parsing if Date object is needed
  console.log(new Date(retrievedProfile.lastLogin));
} else {
  console.log('No user profile found in localStorage.');
}
```

## 7. Storage Limits and Considerations <a name="storage-limits"></a>

### Storage Capacity <a name="storage-capacity"></a>
*   Most browsers provide about 5MB to 10MB of storage per origin for `localStorage` and `sessionStorage` combined or separately (browser-dependent).
*   Exceeding the storage limit will typically cause `setItem()` to throw a `QuotaExceededError` or a similar exception.
    ```javascript
    // try {
    //   localStorage.setItem('largeData', 'a'.repeat(10 * 1024 * 1024)); // Attempt to store 10MB
    // } catch (e) {
    //   if (e.name === 'QuotaExceededError' || e.name === 'NS_ERROR_DOM_QUOTA_REACHED') {
    //     console.error('Storage quota exceeded!');
    //   } else {
    //     console.error('Error storing data:', e);
    //   }
    // }
    ```

### Synchronous Operations <a name="synchronous-operations"></a>
All Web Storage API methods (`setItem`, `getItem`, `removeItem`, `clear`) are **synchronous**. This means they block the main JavaScript thread until the operation completes. For small amounts of data, this is usually fine, but frequent or large operations can impact performance and responsiveness of your web page. For more complex client-side storage needs, consider asynchronous APIs like IndexedDB.

### Security (Same-Origin Policy) <a name="storage-security"></a>
*   Web Storage is subject to the **same-origin policy**. This means data stored by one origin (protocol + hostname + port) cannot be accessed by scripts from a different origin.
*   This prevents malicious scripts on other sites from accessing data stored by your application.

### Data Sensitivity <a name="data-sensitivity"></a>
*   **Do not store sensitive information** (like passwords, private API keys, or personal identification information) in `localStorage` or `sessionStorage`.
*   Data in Web Storage is stored as plain text on the user's computer and can be accessed by any script running on the same origin (including potentially malicious browser extensions or XSS vulnerabilities).
*   For sensitive session tokens, HTTP-only cookies are generally a more secure option as they are not accessible via JavaScript.

## 8. The `storage` Event <a name="storage-event"></a>
When data in `localStorage` is changed (added, modified, or deleted) in one browser tab or window, a `storage` event is fired on other tabs/windows from the same origin.
This event is **not fired on the same page that made the change**.
`sessionStorage` changes do not trigger the `storage` event because it's tab-specific.

```javascript
// In another tab/window from the same origin:
// window.addEventListener('storage', (event) => {
//   console.log('Storage event detected!');
//   console.log('Key changed:', event.key);
//   console.log('Old value:', event.oldValue);
//   console.log('New value:', event.newValue);
//   console.log('URL of page that made change:', event.url);
//   console.log('Storage area:', event.storageArea); // The localStorage object

//   if (event.key === 'theme') {
//     // Update theme in this tab based on change from another tab
//     document.body.className = event.newValue;
//   }
// });

// In the tab making the change:
// localStorage.setItem('theme', 'dark'); // This would trigger the event in other tabs.
```

## 9. Web Storage vs. Cookies <a name="storage-vs-cookies"></a>
| Feature             | Web Storage (`localStorage`/`sessionStorage`) | Cookies                                     |
|---------------------|-----------------------------------------------|---------------------------------------------|
| **Purpose**         | Client-side storage                           | Client-side storage, server communication   |
| **Sent to Server**  | No, not automatically                         | Yes, with every HTTP request to the domain  |
| **Capacity**        | ~5-10MB per origin                            | ~4KB per cookie, ~20 cookies per domain     |
| **Accessibility**   | JavaScript only (client-side)                 | Server-side and client-side (unless HttpOnly)|
| **API**             | Simpler, synchronous API (`setItem`, `getItem`)| More complex (`document.cookie`), or server-set|
| **Expiration**      | `localStorage`: none; `sessionStorage`: on tab close | Manually set expiration dates/times       |
| **Security (XSS)**  | Vulnerable if XSS exists                      | HttpOnly flag can mitigate JS access        |

Web Storage is generally preferred for client-side storage needs where data doesn't need to be sent to the server with every request.

## 10. Key Takeaways <a name="key-takeaways-web-storage"></a>
*   Web Storage API provides `localStorage` (persistent) and `sessionStorage` (session-based) for client-side data storage.
*   Both store data as key/value pairs, where keys and values **must be strings**.
*   Use `JSON.stringify()` and `JSON.parse()` to store and retrieve objects/arrays.
*   `localStorage` data persists across browser sessions; `sessionStorage` data is cleared when the tab closes.
*   `localStorage` is shared between tabs of the same origin; `sessionStorage` is tab-specific.
*   The API is synchronous, which can have performance implications for large data.
*   Be mindful of storage limits (around 5-10MB) and do not store sensitive information.
*   The `storage` event allows cross-tab communication for `localStorage` changes.

---

⬅️ [Back to JSON](../13-js-json/README.md) | ➡️ [Next Section: Fetch API](../15-js-fetch-api/README.md)
