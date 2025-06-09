# 01. JavaScript Variables and Data Types

Variables are fundamental to programming. They act as named containers for storing data values that your program can use and manipulate. JavaScript, being a dynamically typed language, has flexible ways of handling variables and a rich set of data types.

## Table of Contents
1.  [What is a Variable?](#what-is-variable)
2.  [Declaring Variables](#declaring-variables-js)
    *   [`var`](#var-keyword)
    *   [`let`](#let-keyword)
    *   [`const`](#const-keyword)
    *   [Variable Hoisting](#hoisting-js)
3.  [Variable Naming Rules and Conventions](#variable-naming)
4.  [JavaScript Data Types](#js-data-types)
    *   [Primitive Data Types](#primitive-types)
        *   [String](#string-type)
        *   [Number](#number-type)
        *   [BigInt](#bigint-type)
        *   [Boolean](#boolean-type)
        *   [Undefined](#undefined-type)
        *   [Null](#null-type)
        *   [Symbol](#symbol-type)
    *   [Complex/Reference Data Type](#complex-types)
        *   [Object](#object-type)
5.  [The `typeof` Operator](#typeof-operator)
6.  [Dynamic Typing](#dynamic-typing-js)
7.  [Key Takeaways](#key-takeaways-vars-types)

## 1. What is a Variable? <a name="what-is-variable"></a>
In JavaScript, a variable is a symbolic name for a value. You can think of variables as labeled jars where you can store information. The information inside the jar (the value) can change, but the label (the variable name) remains the same.

Variables allow you to:
*   Store data for later use.
*   Refer to data by a descriptive name.
*   Manipulate and update data as your program runs.

## 2. Declaring Variables <a name="declaring-variables-js"></a>
Before you can use a variable in JavaScript, you must declare it. JavaScript provides three keywords for declaring variables: `var`, `let`, and `const`.

### `var` <a name="var-keyword"></a>
Historically, `var` was the primary way to declare variables. Variables declared with `var` are function-scoped or globally-scoped.

*   **Scope**: If declared inside a function, it's local to that function. If declared outside any function, it's global.
*   **Hoisting**: `var` declarations are hoisted to the top of their scope (function or global) and initialized with `undefined`.
*   **Re-declaration**: You can re-declare `var` variables in the same scope without error.

```javascript
var name = "Alice";
var age = 30;

function greet() {
  var message = "Hello!"; // message is function-scoped
  console.log(message, name);
}
greet(); // Outputs: Hello! Alice
// console.log(message); // Error: message is not defined outside the function

var name = "Bob"; // Re-declaration is allowed
console.log(name); // Outputs: Bob
```
**Usage Note**: Due to its sometimes confusing behavior with hoisting and scope (especially block scope), `var` is generally discouraged in modern JavaScript in favor of `let` and `const`.

### `let` <a name="let-keyword"></a>
Introduced in ES6 (ECMAScript 2015), `let` provides block-scoped variables.
A block is any code enclosed in curly braces `{}`, like in `if` statements, `for` loops, or standalone blocks.

*   **Scope**: `let` variables are block-scoped. They are only accessible within the block they are defined in.
*   **Hoisting**: `let` declarations are hoisted to the top of their block, but they are not initialized. Accessing them before declaration results in a `ReferenceError` (this is often called the "Temporal Dead Zone").
*   **Re-declaration**: You cannot re-declare `let` variables in the same scope.

```javascript
let count = 0;

if (true) {
  let message = "Inside block"; // message is block-scoped
  console.log(message);      // Outputs: Inside block
  console.log(count);        // Outputs: 0 (count is accessible from outer scope)
  count = 1;
}

// console.log(message); // Error: message is not defined here
console.log(count);        // Outputs: 1

// let count = 5; // Error: Identifier 'count' has already been declared
```

### `const` <a name="const-keyword"></a>
Also introduced in ES6, `const` is used to declare constants. These are variables whose values cannot be reassigned after initialization. Like `let`, `const` variables are block-scoped.

*   **Scope**: `const` variables are block-scoped.
*   **Hoisting**: Similar to `let`, `const` declarations are hoisted but not initialized (Temporal Dead Zone).
*   **Re-declaration**: You cannot re-declare `const` variables in the same scope.
*   **Assignment**: Must be initialized at the time of declaration. Cannot be reassigned.

```javascript
const PI = 3.14159;
// PI = 3.14; // Error: Assignment to constant variable.

const person = {
  name: "Zoe",
  age: 25
};

// While the 'person' variable itself cannot be reassigned,
// the properties of the object it holds can be changed.
person.age = 26;
console.log(person); // Outputs: { name: "Zoe", age: 26 }

// person = { name: "Alex" }; // Error: Assignment to constant variable.

if (true) {
  const MAX_USERS = 100;
  console.log(MAX_USERS);
}
// console.log(MAX_USERS); // Error: MAX_USERS is not defined here
```
**Best Practice**: Use `const` by default for variables that shouldn't be reassigned. Use `let` for variables whose values might need to change (like loop counters or variables holding changing state).

### Variable Hoisting <a name="hoisting-js"></a>
Hoisting is JavaScript's default behavior of moving declarations to the top of their current scope (their block scope for `let` and `const`, or function/global scope for `var`).

*   **`var` Hoisting**: Declarations are hoisted and initialized with `undefined`.
    ```javascript
    console.log(myVar); // Outputs: undefined (due to hoisting)
    var myVar = 5;
    console.log(myVar); // Outputs: 5
    ```
    This is equivalent to:
    ```javascript
    var myVar;          // Declaration hoisted and initialized with undefined
    console.log(myVar); // undefined
    myVar = 5;          // Assignment happens here
    console.log(myVar); // 5
    ```

*   **`let` and `const` Hoisting**: Declarations are hoisted, but *not* initialized. Accessing them before the line of declaration results in a `ReferenceError`. The period from the start of the block until the declaration is called the Temporal Dead Zone (TDZ).
    ```javascript
    // console.log(myLet); // ReferenceError: Cannot access 'myLet' before initialization (TDZ)
    let myLet = 10;
    console.log(myLet); // Outputs: 10
    ```

## 3. Variable Naming Rules and Conventions <a name="variable-naming"></a>
*   **Rules (must be followed):**
    *   Names can contain letters, digits, underscores (`_`), and dollar signs (`$`).
    *   Names must begin with a letter, an underscore (`_`), or a dollar sign (`$`). They cannot start with a digit.
    *   Names are case-sensitive (`myVariable` and `myvariable` are different).
    *   Reserved keywords (like `let`, `const`, `var`, `function`, `if`, etc.) cannot be used as variable names.

*   **Conventions (good practices):**
    *   **Camel Case**: `myVariableName` (most common for variables and functions).
    *   **Pascal Case**: `MyVariableName` (often used for class names).
    *   **Snake Case**: `my_variable_name` (less common in JS, more in Python/Ruby).
    *   **Constants**: `ALL_CAPS_WITH_UNDERSCORES` (e.g., `MAX_SIZE`, `API_KEY`).
    *   Use descriptive and meaningful names that indicate the variable's purpose.
        ```javascript
        // Good
        let userName = "JohnDoe";
        let itemsInCart = 3;
        const MAX_LOGIN_ATTEMPTS = 5;

        // Less clear
        let x = "JohnDoe";
        let i = 3;
        ```

## 4. JavaScript Data Types <a name="js-data-types"></a>
JavaScript is a dynamically typed language, meaning you don't have to specify the data type of a variable when you declare it. The type is determined automatically at runtime.

JavaScript has two main categories of data types:
1.  **Primitive Data Types**
2.  **Complex (or Reference) Data Type** (Object)

### Primitive Data Types <a name="primitive-types"></a>
Primitive types are immutable (their values cannot be changed once created; operations on them create new values) and are stored directly in the location the variable accesses.

#### String <a name="string-type"></a>
Represents textual data. Strings are enclosed in single quotes (`'...'`), double quotes (`"..."`), or backticks (`` `...` `` - template literals).
```javascript
let greeting = "Hello, world!";
let name = 'Alice';
let message = `Welcome, ${name}!`; // Template literal with interpolation
console.log(typeof greeting); // "string"
```

#### Number <a name="number-type"></a>
Represents numeric values, including integers and floating-point numbers. JavaScript has only one number type.
It also includes special numeric values: `Infinity`, `-Infinity`, and `NaN` (Not a Number).
```javascript
let age = 30;
let price = 19.99;
let temperature = -5;
let result = 0 / 0; // NaN
console.log(typeof age); // "number"
console.log(typeof NaN); // "number"
```

#### BigInt <a name="bigint-type"></a>
Represents whole numbers larger than the maximum safe integer for the `Number` type (2<sup>53</sup> - 1). BigInts are created by appending `n` to the end of an integer or by calling the `BigInt()` constructor.
```javascript
let veryLargeNumber = 9007199254740991n; // Note the 'n' at the end
let anotherBigInt = BigInt("9007199254740992");
console.log(typeof veryLargeNumber); // "bigint"
```

#### Boolean <a name="boolean-type"></a>
Represents logical values: `true` or `false`.
```javascript
let isActive = true;
let isLoggedIn = false;
console.log(typeof isActive); // "boolean"
```

#### Undefined <a name="undefined-type"></a>
A variable that has been declared but not yet assigned a value has the type `undefined` and the value `undefined`.
```javascript
let city;
console.log(city);        // Outputs: undefined
console.log(typeof city); // "undefined"
```

#### Null <a name="null-type"></a>
Represents the intentional absence of any object value. It's a primitive value that signifies "no value" or "empty." It must be explicitly assigned.
```javascript
let user = null;
console.log(user);        // Outputs: null
console.log(typeof user); // "object" (This is a long-standing quirk in JavaScript!)
```
**Note on `typeof null`**: `typeof null` returning `"object"` is a historical bug that hasn't been fixed to avoid breaking existing code. Conceptually, `null` is a primitive type.

#### Symbol <a name="symbol-type"></a>
Introduced in ES6. Symbols are unique and immutable primitive values, often used as keys for object properties to avoid naming collisions.
```javascript
const id = Symbol('description for debug');
const anotherId = Symbol('description for debug');
console.log(id === anotherId); // false (symbols are unique)
console.log(typeof id);      // "symbol"

let userProfile = {
  [id]: 12345
};
console.log(userProfile[id]); // 12345
```

### Complex/Reference Data Type <a name="complex-types"></a>

#### Object <a name="object-type"></a>
Objects are collections of key-value pairs (properties). They are mutable and are reference types, meaning variables store a reference (or pointer) to the object's location in memory, not the object itself.

Arrays, Functions, Dates, RegExp, etc., are all specialized types of objects in JavaScript.
```javascript
// Object literal
let person = {
  firstName: "John",
  lastName: "Doe",
  age: 30,
  isStudent: false
};
console.log(typeof person); // "object"

// Array (a type of object)
let colors = ["red", "green", "blue"];
console.log(typeof colors); // "object" (specifically, Array.isArray(colors) would be true)

// Function (a type of object)
function sayHello() {
  console.log("Hello!");
}
console.log(typeof sayHello); // "function" (functions are callable objects)

// Date (a type of object)
let today = new Date();
console.log(typeof today); // "object"
```

## 5. The `typeof` Operator <a name="typeof-operator"></a>
The `typeof` operator returns a string indicating the type of its operand.

```javascript
console.log(typeof "Hello");    // "string"
console.log(typeof 123);        // "number"
console.log(typeof 123n);       // "bigint"
console.log(typeof true);       // "boolean"
console.log(typeof undefined);  // "undefined"
console.log(typeof null);       // "object" (the historical quirk)
console.log(typeof Symbol("s"));// "symbol"
console.log(typeof {a: 1});     // "object"
console.log(typeof [1, 2]);     // "object"
console.log(typeof function(){});// "function"
```

## 6. Dynamic Typing <a name="dynamic-typing-js"></a>
JavaScript is a dynamically typed language. This means:
*   You do not need to declare the type of a variable in advance.
*   The type of a variable can change during the execution of the program.

```javascript
let myDynamicVar = "I am a string";
console.log(myDynamicVar, typeof myDynamicVar); // "I am a string", "string"

myDynamicVar = 100;
console.log(myDynamicVar, typeof myDynamicVar); // 100, "number"

myDynamicVar = true;
console.log(myDynamicVar, typeof myDynamicVar); // true, "boolean"
```
While dynamic typing offers flexibility, it can also lead to unexpected behavior if types are not managed carefully.

## 7. Key Takeaways <a name="key-takeaways-vars-types"></a>
*   Variables are named containers for data.
*   Use `let` for variables that may be reassigned and `const` for variables that should not be reassigned (constants). Avoid `var` in modern JavaScript.
*   `let` and `const` are block-scoped and are affected by the Temporal Dead Zone.
*   Follow naming rules and conventions (camelCase is common) for clarity.
*   JavaScript has primitive data types (String, Number, BigInt, Boolean, Undefined, Null, Symbol) and a complex data type (Object).
*   The `typeof` operator helps determine a variable's data type.
*   JavaScript is dynamically typed, allowing variable types to change at runtime.

Understanding variables and data types is crucial for writing any JavaScript program. They are the building blocks for storing and manipulating information.

---

⬅️ [Back to Introduction to JavaScript](../00-introduction-to-javascript/README.md) | ➡️ [Next Section: JavaScript Operators](../02-js-operators/README.md)
