# 19. ES6+ Features Overview

ECMAScript 2015 (ES6) marked a significant evolution for JavaScript, introducing a host of new features and syntactic improvements. Subsequent yearly releases (ES2016, ES2017, etc., collectively often referred to as ES6+) have continued to enhance the language. This tutorial provides an overview of some key ES6+ features, many of which simplify common tasks and promote cleaner, more readable code.

Some features like Arrow Functions, Classes, Modules, and Promises have been covered in detail in previous tutorials but will be briefly recapped here for completeness.

## Table of Contents
1.  [`let` and `const`](#let-const)
2.  [Arrow Functions](#arrow-functions)
3.  [Template Literals](#template-literals)
4.  [Destructuring Assignment](#destructuring)
    *   [Object Destructuring](#object-destructuring)
    *   [Array Destructuring](#array-destructuring)
5.  [Default Parameters](#default-parameters)
6.  [Rest Parameter and Spread Syntax](#rest-spread)
    *   [Rest Parameter (`...rest`)](#rest-parameter)
    *   [Spread Syntax (`...iterable`)](#spread-syntax)
7.  [ES6 Modules (Recap)](#es6-modules)
8.  [ES6 Classes (Recap)](#es6-classes)
9.  [Promises and Async/Await (Recap)](#promises-async-await)
10. [Iterators and Generators](#iterators-generators)
    *   [Iterators](#iterators)
    *   [Generators (`function*` and `yield`)](#generators)
11. [`for...of` Loop (Recap)](#for-of-loop)
12. [New Data Structures](#new-data-structures)
    *   [`Map`](#map-object)
    *   [`Set`](#set-object)
    *   [`WeakMap` and `WeakSet`](#weakmap-weakset)
13. [Symbols](#symbols)
14. [Enhanced Object Literals](#enhanced-object-literals)
15. [New String Methods](#new-string-methods)
16. [New Array Methods](#new-array-methods)
17. [New Object Methods](#new-object-methods)
18. [Optional Chaining (`?.`) (ES2020)](#optional-chaining)
19. [Nullish Coalescing Operator (`??`) (ES2020)](#nullish-coalescing)
20. [`BigInt` (ES2020)](#bigint)
21. [Dynamic `import()` (ES2020)](#dynamic-import)
22. [Key Takeaways](#key-takeaways-es6-features)

## 1. `let` and `const` <a name="let-const"></a>
Block-scoped variable declarations that replace `var` in most cases.
*   `let`: Declares a block-scoped variable that can be reassigned.
*   `const`: Declares a block-scoped variable that must be initialized and cannot be reassigned (though its content can be mutated if it's an object or array).
```javascript
let count = 0;
count = 1;

const PI = 3.14159;
// PI = 3; // Error: Assignment to constant variable.

const person = { name: "Alice" };
person.name = "Bob"; // Allowed, mutating the object
// person = { name: "Charlie" }; // Error, reassigning const
```
(Covered in [Tutorial 01: Introduction](../01-js-introduction-and-setup/README.md))

## 2. Arrow Functions <a name="arrow-functions"></a>
A concise syntax for writing function expressions. They lexically bind `this`, meaning they inherit `this` from the surrounding (enclosing) scope.
```javascript
const add = (a, b) => a + b;
const square = x => x * x;

const obj = {
  value: 10,
  getValue: function() {
    setTimeout(() => {
      console.log(this.value); // 'this' refers to 'obj'
    }, 100);
  }
};
obj.getValue(); // 10
```
(Covered in [Tutorial 06: Functions](../06-js-functions/README.md) and [Tutorial 17: `this` Deep Dive](../17-js-object-methods-this-advanced/README.md))

## 3. Template Literals <a name="template-literals"></a>
Allow embedded expressions and multi-line strings using backticks (`` ` ``).
```javascript
const name = "World";
const greeting = `Hello, ${name}!`; // "Hello, World!"

const multiLine = `This is
a multi-line
string.`;
```

## 4. Destructuring Assignment <a name="destructuring"></a>
Allows unpacking values from arrays or properties from objects into distinct variables.

### Object Destructuring <a name="object-destructuring"></a>
```javascript
const user = { id: 1, username: "dev", email: "dev@example.com" };
const { username, email } = user;
console.log(username); // "dev"
console.log(email);    // "dev@example.com"

// With different variable names and default values
const { username: uName, isAdmin = false } = user;
console.log(uName); // "dev"
console.log(isAdmin); // false
```

### Array Destructuring <a name="array-destructuring"></a>
```javascript
const numbers = [1, 2, 3, 4, 5];
const [first, second, , fourth] = numbers;
console.log(first);  // 1
console.log(second); // 2
console.log(fourth); // 4

// Swapping variables
let a = 10, b = 20;
[a, b] = [b, a];
console.log(a, b); // 20 10
```

## 5. Default Parameters <a name="default-parameters"></a>
Allows named parameters to be initialized with default values if no value or `undefined` is passed.
```javascript
function greet(name = "Guest", message = "Hello") {
  console.log(`${message}, ${name}!`);
}
greet("Alice");      // "Hello, Alice!"
greet();             // "Hello, Guest!"
greet(undefined, "Hi"); // "Hi, Guest!"
```

## 6. Rest Parameter and Spread Syntax <a name="rest-spread"></a>
Both use the `...` syntax but in different contexts.

### Rest Parameter (`...rest`) <a name="rest-parameter"></a>
Collects all remaining arguments passed to a function into an array.
```javascript
function sumAll(...numbers) {
  return numbers.reduce((acc, current) => acc + current, 0);
}
console.log(sumAll(1, 2, 3));    // 6
console.log(sumAll(10, 20, 30, 40)); // 100
```

### Spread Syntax (`...iterable`) <a name="spread-syntax"></a>
Expands an iterable (like an array or string) into individual elements. Useful for function calls, array literals, and object literals (ES2018 for objects).
```javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

const nameChars = [...'hello']; // ['h', 'e', 'l', 'l', 'o']

const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 }; // { a: 1, b: 2, c: 3 }

Math.max(...arr1); // 3
```

## 7. ES6 Modules (Recap) <a name="es6-modules"></a>
Standardized module system using `import` and `export` statements.
```javascript
// lib.js
export const PI = 3.14;
export function calculateCircumference(radius) {
  return 2 * PI * radius;
}

// main.js
import { PI, calculateCircumference } from './lib.js';
console.log(calculateCircumference(10)); // 62.8
```
(Covered in [Tutorial 11: JavaScript Modules](../11-js-modules/README.md))

## 8. ES6 Classes (Recap) <a name="es6-classes"></a>
Syntactic sugar over JavaScript's existing prototype-based inheritance. Provides a cleaner way to create objects and deal with inheritance.
```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Call parent constructor
    this.breed = breed;
  }
  speak() {
    console.log(`${this.name} barks.`);
  }
}
const myDog = new Dog("Buddy", "Labrador");
myDog.speak(); // "Buddy barks."
```
(Covered in [Tutorial 08: Objects and Prototypes](../08-js-objects-prototypes/README.md) and [Tutorial 18: Prototypes and Inheritance In-Depth](../18-js-prototypes-inheritance-advanced/README.md))

## 9. Promises and Async/Await (Recap) <a name="promises-async-await"></a>
*   **Promises**: Objects representing the eventual completion (or failure) of an asynchronous operation.
*   **`async`/`await` (ES2017)**: Syntactic sugar built on top of Promises, making asynchronous code look and behave a bit more like synchronous code.
```javascript
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve("Data fetched!"), 1000);
  });
}

async function processData() {
  try {
    const data = await fetchData();
    console.log(data);
  } catch (error) {
    console.error("Error:", error);
  }
}
processData(); // "Data fetched!" (after 1 second)
```
(Covered in [Tutorial 10: Asynchronous JavaScript](../10-js-async/README.md))

## 10. Iterators and Generators <a name="iterators-generators"></a>

### Iterators <a name="iterators"></a>
An object that knows how to access items from a collection one at a time, while keeping track of its current position within that sequence. It implements the iterator protocol (has a `next()` method).

### Generators (`function*` and `yield`) <a name="generators"></a>
A special type of function that can be paused and resumed, allowing for the creation of iterators in a more straightforward way. The `yield` keyword pauses execution and returns a value.
```javascript
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();
console.log(gen.next().value); // 1
console.log(gen.next().value); // 2
console.log(gen.next().value); // 3
console.log(gen.next().done);  // true
```

## 11. `for...of` Loop (Recap) <a name="for-of-loop"></a>
Iterates over iterable objects (Arrays, Strings, Maps, Sets, results of generator functions, etc.), invoking a custom iteration hook with statements to be executed for the value of each distinct property.
```javascript
const colors = ['red', 'green', 'blue'];
for (const color of colors) {
  console.log(color);
}
// red
// green
// blue
```
(Covered in [Tutorial 05: Loops](../05-js-loops/README.md))

## 12. New Data Structures <a name="new-data-structures"></a>

### `Map` <a name="map-object"></a>
A collection of key-value pairs where keys can be of any type. Remembers the original insertion order of the keys.
```javascript
const myMap = new Map();
myMap.set('name', 'Alice');
myMap.set(1, 'one');
const objKey = {};
myMap.set(objKey, 'object key value');

console.log(myMap.get('name')); // "Alice"
console.log(myMap.size);      // 3
for (const [key, value] of myMap) {
  console.log(key, value);
}
```

### `Set` <a name="set-object"></a>
A collection of unique values. Values can be of any type.
```javascript
const mySet = new Set([1, 2, 2, 3, 4, 4, 4]);
mySet.add(5);
mySet.add(1); // Ignored, already present

console.log(mySet); // Set(5) { 1, 2, 3, 4, 5 }
console.log(mySet.has(3)); // true
for (const value of mySet) {
  console.log(value);
}
```

### `WeakMap` and `WeakSet` <a name="weakmap-weakset"></a>
Similar to `Map` and `Set`, but they hold "weak" references to their keys (for `WeakMap`) or values (for `WeakSet`). This means if there are no other references to an object stored as a key/value, it can be garbage collected. Useful for memory management in certain scenarios.

## 13. Symbols <a name="symbols"></a>
A unique and immutable primitive data type, often used as keys for object properties to avoid naming collisions.
```javascript
const idSymbol = Symbol('id');
const userObj = {
  name: 'John',
  [idSymbol]: 123
};
console.log(userObj[idSymbol]); // 123
console.log(Object.keys(userObj)); // ['name'] (Symbols are not enumerable by default)
console.log(Object.getOwnPropertySymbols(userObj)); // [Symbol(id)]
```

## 14. Enhanced Object Literals <a name="enhanced-object-literals"></a>
Shorthand syntax for defining object properties and methods.
```javascript
const name = "Laptop";
const price = 1200;

const product = {
  // Shorthand for property names
  name,
  price,
  // Shorthand for method definition
  displayInfo() {
    console.log(`${this.name}: $${this.price}`);
  },
  // Computed property names
  ['prop_' + (() => 'ID')()]: 'P123'
};

product.displayInfo(); // "Laptop: $1200"
console.log(product.prop_ID); // "P123"
```

## 15. New String Methods <a name="new-string-methods"></a>
*   `startsWith(searchString, position?)`: Checks if a string starts with another string.
*   `endsWith(searchString, endPosition?)`: Checks if a string ends with another string.
*   `includes(searchString, position?)`: Checks if a string contains another string.
*   `repeat(count)`: Returns a new string with a specified number of copies of the string concatenated together.
*   `padStart(targetLength, padString?)` (ES2017): Pads the current string from the start with another string until the resulting string reaches the given length.
*   `padEnd(targetLength, padString?)` (ES2017): Pads the current string from the end.
*   `trimStart()`, `trimEnd()` (ES2019): Remove whitespace from the beginning/end of a string.

## 16. New Array Methods <a name="new-array-methods"></a>
*   `Array.from(arrayLikeOrIterable, mapFn?, thisArg?)`: Creates a new Array instance from an array-like or iterable object.
*   `Array.of(...items)`: Creates a new Array instance with a variable number of arguments, regardless of number or type of the arguments.
*   `find(callbackFn, thisArg?)`: Returns the value of the first element in the array that satisfies the provided testing function.
*   `findIndex(callbackFn, thisArg?)`: Returns the index of the first element in the array that satisfies the provided testing function.
*   `includes(valueToFind, fromIndex?)` (ES2016): Determines whether an array includes a certain value among its entries.
*   `copyWithin(target, start?, end?)`: Shallow copies part of an array to another location in the same array and returns it without modifying its length.
*   `fill(value, start?, end?)`: Fills all the elements of an array from a start index to an end index with a static value.
*   `flat(depth?)` (ES2019): Creates a new array with all sub-array elements concatenated into it recursively up to the specified depth.
*   `flatMap(callbackFn, thisArg?)` (ES2019): Maps each element using a mapping function, then flattens the result into a new array.

## 17. New Object Methods <a name="new-object-methods"></a>
*   `Object.assign(target, ...sources)`: Copies all enumerable own properties from one or more source objects to a target object.
*   `Object.keys(obj)`: Returns an array of a given object's own enumerable property names.
*   `Object.values(obj)` (ES2017): Returns an array of a given object's own enumerable property values.
*   `Object.entries(obj)` (ES2017): Returns an array of a given object's own enumerable string-keyed property [key, value] pairs.
*   `Object.getOwnPropertyDescriptors(obj)` (ES2017): Returns all own property descriptors of a given object.
*   `Object.fromEntries(iterable)` (ES2019): Transforms a list of key-value pairs into an object.

## 18. Optional Chaining (`?.`) (ES2020) <a name="optional-chaining"></a>
Allows reading the value of a property located deep within a chain of connected objects without having to explicitly validate that each reference in the chain is valid. If a reference is `null` or `undefined`, the expression short-circuits and evaluates to `undefined`.
```javascript
const userProfile = {
  details: {
    address: {
      street: "123 Main St"
    }
  }
  // contact: { phone: '555-1234' }
};

console.log(userProfile.details?.address?.street); // "123 Main St"
console.log(userProfile.contact?.phone);           // undefined
console.log(userProfile.getPreferences?.());       // undefined (if getPreferences is not a function)
```

## 19. Nullish Coalescing Operator (`??`) (ES2020) <a name="nullish-coalescing"></a>
A logical operator that returns its right-hand side operand when its left-hand side operand is `null` or `undefined`, and otherwise returns its left-hand side operand.
```javascript
const value1 = null;
const value2 = undefined;
const value3 = 0;
const value4 = '';
const value5 = 'Hello';

console.log(value1 ?? 'Default'); // "Default"
console.log(value2 ?? 'Default'); // "Default"
console.log(value3 ?? 'Default'); // 0 (0 is not nullish)
console.log(value4 ?? 'Default'); // "" (empty string is not nullish)
console.log(value5 ?? 'Default'); // "Hello"

// Contrast with || (OR operator)
console.log(value3 || 'Default'); // "Default" (0 is falsy)
```

## 20. `BigInt` (ES2020) <a name="bigint"></a>
A new numeric primitive that can represent integers with arbitrary precision, larger than the `2^53 - 1` limit for `Number`.
```javascript
const largeNumber = 9007199254740991n; // Note the 'n' suffix
const anotherLarge = BigInt("9007199254740992");
console.log(largeNumber + 1n); // 9007199254740992n
// Cannot mix BigInt with Number in most operations
// console.log(largeNumber + 1); // TypeError
```

## 21. Dynamic `import()` (ES2020) <a name="dynamic-import"></a>
Allows loading ES modules dynamically. `import()` returns a Promise.
```javascript
async function loadModule() {
  try {
    const module = await import('./utils.js');
    module.doSomething();
  } catch (error) {
    console.error("Failed to load module:", error);
  }
}
// loadModule();
```
(Briefly mentioned in [Tutorial 11: JavaScript Modules](../11-js-modules/README.md))

## 22. Key Takeaways <a name="key-takeaways-es6-features"></a>
*   ES6 and subsequent versions have significantly modernized JavaScript, making it more powerful and expressive.
*   Features like `let`/`const`, arrow functions, template literals, destructuring, and classes are now fundamental to modern JavaScript development.
*   New data structures (`Map`, `Set`), methods for built-in objects (Array, String, Object), and operators (`?.`, `??`) simplify common programming tasks.
*   Understanding these features is crucial for writing efficient, readable, and maintainable JavaScript code.

---

⬅️ [Back to Prototypes and Inheritance In-Depth](../18-js-prototypes-inheritance-advanced/README.md) | ➡️ [Next Section: JavaScript Best Practices & Coding Conventions](../20-js-best-practices/README.md) (Placeholder)
