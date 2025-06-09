# 13. JSON (JavaScript Object Notation)

JSON (JavaScript Object Notation) is a lightweight, text-based, data-interchange format. It is easy for humans to read and write and easy for machines to parse and generate. Although derived from JavaScript, JSON is language-independent, with parsers available for many programming languages, making it a popular choice for client-server communication and data storage.

## Table of Contents
1.  [What is JSON?](#what-is-json)
    *   [Common Uses](#common-uses-json)
2.  [JSON Syntax Rules](#json-syntax-rules)
    *   [Data Types](#json-data-types)
    *   [Structure (Objects and Arrays)](#json-structure)
    *   [Key-Value Pairs](#json-key-value-pairs)
3.  [Working with JSON in JavaScript](#working-with-json-js)
    *   [`JSON.parse()`: Converting JSON String to JavaScript Object](#json-parse)
        *   [Handling Parsing Errors](#json-parse-errors)
        *   [The Reviver Function](#json-parse-reviver)
    *   [`JSON.stringify()`: Converting JavaScript Value to JSON String](#json-stringify)
        *   [The Replacer Function/Array](#json-stringify-replacer)
        *   [The `space` Argument (Pretty-Printing)](#json-stringify-space)
        *   [The `toJSON()` Method](#json-stringify-tojson)
        *   [How `undefined`, Functions, and Symbols are Treated](#json-stringify-specials)
4.  [Common Use Cases](#json-common-uses)
    *   [Web APIs (Fetching and Sending Data)](#json-apis)
    *   [Configuration Files](#json-config-files)
    *   [Storing Data (e.g., `localStorage`)](#json-localstorage)
5.  [Limitations of JSON](#json-limitations)
6.  [Key Takeaways](#key-takeaways-json)

## 1. What is JSON? <a name="what-is-json"></a>
JSON is a format for structuring data. It's often used when data is sent from a server to a web page or mobile app, or vice-versa. It's designed to be human-readable and easy for computers to process.

### Common Uses <a name="common-uses-json"></a>
*   **Data Interchange**: Transmitting data between a client (e.g., browser, mobile app) and a server.
*   **Configuration Files**: Storing application settings.
*   **Data Storage**: Storing structured data in NoSQL databases or local storage.
*   **APIs**: Most modern web APIs accept requests and send responses in JSON format.

## 2. JSON Syntax Rules <a name="json-syntax-rules"></a>
JSON syntax is a subset of JavaScript object literal syntax, but with stricter rules:

### Data Types <a name="json-data-types"></a>
JSON supports the following data types:
*   **String**: A sequence of Unicode characters enclosed in **double quotes** (e.g., `"hello"`). Single quotes are not allowed.
*   **Number**: An integer or floating-point number (e.g., `100`, `3.14`, `-0.5`, `1.2e10`). No `NaN` or `Infinity`.
*   **Boolean**: `true` or `false` (lowercase).
*   **Array**: An ordered list of values, enclosed in square brackets `[]` (e.g., `[1, "apple", true]`). Values can be of different types.
*   **Object**: An unordered collection of key/value pairs, enclosed in curly braces `{}` (e.g., `{"name": "John", "age": 30}`).
*   **`null`**: Represents an empty value (lowercase).

JSON does **not** support `undefined`, functions, dates (they are typically represented as ISO 8601 strings), comments, or regular expressions.

### Structure (Objects and Arrays) <a name="json-structure"></a>
A JSON document typically starts with an object or an array.
*   **JSON Object**: `{"key1": "value1", "key2": 123, "key3": {"nestedKey": true}}`
*   **JSON Array**: `["apple", "banana", 100, null, {"type": "fruit"}]`

### Key-Value Pairs <a name="json-key-value-pairs"></a>
Inside JSON objects, data is represented in key/value pairs:
*   **Keys**: Must be strings enclosed in **double quotes** (e.g., `"name"`).
*   **Values**: Can be any valid JSON data type (string, number, boolean, array, object, or `null`).
*   A colon `:` separates keys from values.
*   Commas `,` separate key/value pairs within an object and elements within an array.
*   Trailing commas are **not allowed**.

**Example of a valid JSON object:**
```json
{
  "firstName": "Jane",
  "lastName": "Doe",
  "age": 28,
  "isStudent": false,
  "address": {
    "streetAddress": "123 Main St",
    "city": "Anytown"
  },
  "phoneNumbers": [
    {"type": "home", "number": "555-1234"},
    {"type": "work", "number": "555-5678"}
  ],
  "spouse": null
}
```

## 3. Working with JSON in JavaScript <a name="working-with-json-js"></a>
JavaScript provides a global `JSON` object with methods to parse JSON strings and convert JavaScript values to JSON strings.

### `JSON.parse()`: Converting JSON String to JavaScript Object <a name="json-parse"></a>
The `JSON.parse()` method takes a JSON string as input and converts it into a JavaScript object or value.

```javascript
const jsonString = '{"name": "Alice", "age": 30, "city": "Wonderland"}';

const jsObject = JSON.parse(jsonString);

console.log(jsObject.name);  // Output: Alice
console.log(jsObject.age);   // Output: 30
console.log(jsObject.city);  // Output: Wonderland

const jsonArrayString = '[1, "hello", true, null]';
const jsArray = JSON.parse(jsonArrayString);
console.log(jsArray[1]); // Output: hello
```

#### Handling Parsing Errors <a name="json-parse-errors"></a>
If the input string is not valid JSON, `JSON.parse()` will throw a `SyntaxError`.
```javascript
try {
  const invalidJsonString = '{"name": "Bob", age: 40}'; // age key not in double quotes
  const obj = JSON.parse(invalidJsonString);
} catch (error) {
  console.error("Parsing error:", error.name, "-", error.message);
  // Output: Parsing error: SyntaxError - Unexpected token a in JSON at position 15 (or similar)
}
```

#### The Reviver Function <a name="json-parse-reviver"></a>
`JSON.parse()` accepts an optional second argument, a `reviver` function. This function is called for each key and value at every level of the final result. If the reviver function returns a value, that value is used in place of the original value. If it returns `undefined`, the property is deleted from the result.

This is often used to transform values, like converting date strings back into `Date` objects.
```javascript
const jsonWithDate = '{"name": "Event A", "date": "2024-01-15T10:00:00.000Z"}';

const parsedWithReviver = JSON.parse(jsonWithDate, (key, value) => {
  if (key === "date" && typeof value === 'string') {
    return new Date(value); // Convert date string to Date object
  }
  return value; // Return other values unchanged
});

console.log(parsedWithReviver.name);       // Output: Event A
console.log(parsedWithReviver.date);       // Output: Date object (e.g., Mon Jan 15 2024 ...)
console.log(parsedWithReviver.date instanceof Date); // Output: true
```

### `JSON.stringify()`: Converting JavaScript Value to JSON String <a name="json-stringify"></a>
The `JSON.stringify()` method converts a JavaScript object or value into a JSON string.

```javascript
const user = {
  name: "Charlie",
  age: 25,
  isAdmin: true,
  courses: ["Math", "History"],
  address: null
};

const jsonStringOutput = JSON.stringify(user);
console.log(jsonStringOutput);
// Output: {"name":"Charlie","age":25,"isAdmin":true,"courses":["Math","History"],"address":null}
```

#### The Replacer Function/Array <a name="json-stringify-replacer"></a>
`JSON.stringify()` can take an optional second argument, `replacer`, which can be either a function or an array of strings and numbers.

*   **Replacer as an array**: Only properties listed in the array will be included in the JSON string.
    ```javascript
    const product = { id: 101, name: "Laptop", price: 1200, category: "Electronics" };
    const filteredJson = JSON.stringify(product, ["name", "price"]);
    console.log(filteredJson); // Output: {"name":"Laptop","price":1200}
    ```

*   **Replacer as a function**: This function is called for each key and value. If it returns a value, that value is stringified. If it returns `undefined`, the property is excluded from the JSON string.
    ```javascript
    const settings = { theme: "dark", fontSize: 14, userToken: "xyz123abc", internalId: 99 };
    const customJson = JSON.stringify(settings, (key, value) => {
      if (key === "userToken") {
        return undefined; // Exclude userToken
      }
      if (typeof value === 'number') {
        return value * 2; // Double numeric values
      }
      return value; // Return other values unchanged
    });
    console.log(customJson); // Output: {"theme":"dark","fontSize":28,"internalId":198}
    ```

#### The `space` Argument (Pretty-Printing) <a name="json-stringify-space"></a>
The optional third argument, `space`, controls spacing in the output JSON string for readability (pretty-printing).
*   If `space` is a number, it indicates the number of space characters to use as white space for indentation (up to 10).
*   If `space` is a string (e.g., `"\t"`), that string is used for indentation.

```javascript
const data = { name: "Test Data", version: 1, items: [1, 2] };

console.log(JSON.stringify(data, null, 2));
/* Output:
{
  "name": "Test Data",
  "version": 1,
  "items": [
    1,
    2
  ]
}
*/

console.log(JSON.stringify(data, null, "--"));
/* Output:
{
--"name": "Test Data",
--"version": 1,
--"items": [
----1,
----2
--]
}
*/
```

#### The `toJSON()` Method <a name="json-stringify-tojson"></a>
If an object being stringified has a property named `toJSON` that is a function, `JSON.stringify()` calls this `toJSON()` method and stringifies the value returned by it, instead of the original object.

```javascript
const userWithToJson = {
  firstName: "David",
  lastName: "Copperfield",
  birthDate: new Date(Date.UTC(1980, 5, 15)), // June 15, 1980
  toJSON: function() {
    return {
      fullName: `${this.firstName} ${this.lastName}`,
      born: this.birthDate.toISOString().split('T')[0] // YYYY-MM-DD format
    };
  }
};

console.log(JSON.stringify(userWithToJson));
// Output: {"fullName":"David Copperfield","born":"1980-06-15"}

// Note: Date objects inherently have a toJSON method that returns an ISO 8601 string.
const justADate = new Date();
console.log(JSON.stringify(justADate)); // e.g., "2023-10-27T10:30:00.000Z"
```

#### How `undefined`, Functions, and Symbols are Treated <a name="json-stringify-specials"></a>
*   If a value is `undefined`, a function, or a Symbol, it is either omitted (when found in an object) or censored to `null` (when found in an array).
*   `JSON.stringify(undefined)` returns `undefined` (not a JSON string `"undefined"`).
*   `JSON.stringify(function(){})` returns `undefined`.
*   `JSON.stringify(Symbol())` returns `undefined`.

```javascript
const objWithSpecials = {
  a: 1,
  b: undefined,
  c: function() { console.log('hello'); },
  d: Symbol('id'),
  e: [10, undefined, () => {}, Symbol('test')]
};

console.log(JSON.stringify(objWithSpecials));
// Output: {"a":1,"e":[10,null,null,null]}
// Properties b, c, d are omitted.
// undefined, function, Symbol in array 'e' are converted to null.
```

## 4. Common Use Cases <a name="json-common-uses"></a>

### Web APIs (Fetching and Sending Data) <a name="json-apis"></a>
JSON is the de facto standard for data exchange in web APIs.
```javascript
// Fetching data from an API
/*
fetch('https://api.example.com/data')
  .then(response => {
    if (!response.ok) {
      throw new Error('Network response was not ok ' + response.statusText);
    }
    return response.json(); // Parses the JSON response body
  })
  .then(data => {
    console.log('Data from API:', data);
  })
  .catch(error => {
    console.error('Fetch error:', error);
  });

// Sending data to an API
const postData = { username: 'testuser', score: 100 };
fetch('https://api.example.com/submit', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(postData) // Convert JS object to JSON string
})
.then(response => response.json())
.then(result => console.log('Server response:', result))
.catch(error => console.error('Post error:', error));
*/
```

### Configuration Files <a name="json-config-files"></a>
Many applications and tools use JSON files for configuration (e.g., `package.json` in Node.js projects, `tsconfig.json` for TypeScript).
```json
// Example: config.json
{
  "appName": "My Awesome App",
  "version": "1.2.0",
  "apiEndpoint": "https://api.myapp.com",
  "features": {
    "notifications": true,
    "darkMode": false
  }
}
```

### Storing Data (e.g., `localStorage`) <a name="json-localstorage"></a>
Web storage APIs like `localStorage` and `sessionStorage` can only store strings. JSON is used to serialize and deserialize objects for storage.
```javascript
const userPreferences = { theme: 'dark', notifications: 'enabled' };

// Storing data
localStorage.setItem('userPrefs', JSON.stringify(userPreferences));

// Retrieving data
const storedPrefsString = localStorage.getItem('userPrefs');
const retrievedPrefs = storedPrefsString ? JSON.parse(storedPrefsString) : {};

console.log(retrievedPrefs.theme); // dark
```

## 5. Limitations of JSON <a name="json-limitations"></a>
*   **No Comments**: JSON does not support comments. This can be a drawback for configuration files where explanations are helpful.
*   **Strict Syntax**: Keys must be double-quoted, trailing commas are forbidden. This can make manual editing error-prone.
*   **Limited Data Types**: Does not natively support `Date` objects, `RegExp`, `undefined`, functions, or `Symbol`. These need special handling (e.g., converting dates to ISO strings).
*   **No Circular References**: `JSON.stringify()` will throw a `TypeError` if it encounters a circular reference.
    ```javascript
    // const objA = {};
    // const objB = { refA: objA };
    // objA.refB = objB; // Circular reference
    // try {
    //   JSON.stringify(objA);
    // } catch (e) {
    //   console.error(e.name); // TypeError
    // }
    ```

## 6. Key Takeaways <a name="key-takeaways-json"></a>
*   JSON is a lightweight, text-based format for data interchange.
*   It has a simple, strict syntax with basic data types: strings (double-quoted), numbers, booleans, arrays, objects, and `null`.
*   JavaScript's global `JSON` object provides `JSON.parse()` (string to object) and `JSON.stringify()` (object to string).
*   `JSON.parse()` can use a `reviver` function to transform parsed values.
*   `JSON.stringify()` can use `replacer` (array/function) and `space` arguments to customize output.
*   Objects can define a `toJSON()` method to control their JSON representation.
*   `undefined`, functions, and Symbols have special handling during stringification.
*   JSON is widely used in APIs, configuration files, and data storage, despite some limitations.

---

⬅️ [Back to Error Handling](../12-js-error-handling/README.md) | ➡️ [Next Section: Web Storage API (localStorage & sessionStorage)](../14-js-web-storage/README.md)
