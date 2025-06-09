# 03. JavaScript Type Conversion (Type Coercion)

JavaScript is a dynamically typed language, which means variable types are determined at runtime. Often, JavaScript will automatically convert values from one type to another, a process known as **type coercion** (or implicit type conversion). You can also explicitly convert types using built-in functions or operators.

Understanding type conversion is crucial for avoiding common bugs and writing predictable JavaScript code.

## Table of Contents
1.  [Implicit vs. Explicit Conversion](#implicit-vs-explicit)
2.  [Converting to String](#to-string)
    *   [`String()` function](#string-function)
    *   `toString()` method](#tostring-method)
    *   Concatenation with `+` operator](#string-concatenation-conversion)
3.  [Converting to Number](#to-number)
    *   [`Number()` function](#number-function)
    *   Unary `+` operator](#unary-plus-conversion)
    *   `parseInt()` function](#parseint-function)
    *   `parseFloat()` function](#parsefloat-function)
    *   Arithmetic operators (except `+` with strings)](#arithmetic-conversion)
4.  [Converting to Boolean](#to-boolean)
    *   [`Boolean()` function](#boolean-function)
    *   Logical operators (Implicit Coercion)](#logical-coercion)
    *   [Truthy and Falsy Values](#truthy-falsy)
5.  [Type Coercion in Comparisons](#coercion-comparisons)
    *   [Loose Equality (`==`)](#loose-equality-coercion)
    *   [Relational Operators (`<`, `>`, `<=`, `>=`)](#relational-coercion)
6.  [Object to Primitive Conversion](#object-to-primitive)
    *   [The `Symbol.toPrimitive` method](#symbol-toprimitive)
    *   [`valueOf()` and `toString()` methods](#valueof-tostring-conversion)
    *   [Numeric, String, and Default Hints](#conversion-hints)
7.  [Common Pitfalls and Best Practices](#pitfalls-best-practices)
8.  [Key Takeaways](#key-takeaways-type-conversion)

## 1. Implicit vs. Explicit Conversion <a name="implicit-vs-explicit"></a>
*   **Implicit Type Conversion (Coercion)**: JavaScript automatically converts types when an operation involves mixed types. This happens behind the scenes.
    ```javascript
    console.log("5" + 3);   // "53" (3 is coerced to string "3")
    console.log("5" - 3);   // 2    ("5" is coerced to number 5)
    console.log(5 * null);  // 0    (null is coerced to number 0)
    if (23) { /* ... */ } // 23 is coerced to boolean true
    ```
*   **Explicit Type Conversion (Casting)**: You manually convert a value from one type to another using built-in functions or operators.
    ```javascript
    let numStr = "123";
    let num = Number(numStr); // Explicitly convert string to number
    console.log(num); // 123

    let val = 0;
    let boolVal = Boolean(val); // Explicitly convert number to boolean
    console.log(boolVal); // false
    ```

## 2. Converting to String <a name="to-string"></a>

### `String()` function <a name="string-function"></a>
The global `String()` function can convert any value to its string representation.
```javascript
console.log(String(123));        // "123"
console.log(String(true));       // "true"
console.log(String(null));       // "null"
console.log(String(undefined));  // "undefined"
console.log(String({a: 1}));     // "[object Object]"
console.log(String([1, 2]));     // "1,2"
```

### `toString()` method <a name="tostring-method"></a>
Most values (except `null` and `undefined`) have a `toString()` method. For numbers, `toString()` can take an optional `radix` argument (2-36) to convert to different bases.
```javascript
let num = 255;
console.log(num.toString());      // "255" (base 10)
console.log(num.toString(16));    // "ff" (hexadecimal)
console.log(num.toString(2));     // "11111111" (binary)

console.log(true.toString());     // "true"
console.log([1, 2, 3].toString());// "1,2,3"
console.log({key: 'val'}.toString()); // "[object Object]"

// (null).toString();      // TypeError: Cannot read properties of null (reading 'toString')
// (undefined).toString(); // TypeError: Cannot read properties of undefined (reading 'toString')
```

### Concatenation with `+` operator <a name="string-concatenation-conversion"></a>
If one of the operands of the `+` operator is a string, the other operand will be converted to a string, and concatenation will be performed.
```javascript
console.log("The answer is " + 42); // "The answer is 42"
console.log(true + " is a boolean"); // "true is a boolean"
console.log(null + " value");        // "null value"
```

## 3. Converting to Number <a name="to-number"></a>

### `Number()` function <a name="number-function"></a>
The global `Number()` function attempts to convert a value to a number.
```javascript
console.log(Number("123"));      // 123
console.log(Number("3.14"));     // 3.14
console.log(Number(" "));         // 0 (empty string or string with only spaces)
console.log(Number("hello"));    // NaN (Not a Number)
console.log(Number(true));       // 1
console.log(Number(false));      // 0
console.log(Number(null));       // 0
console.log(Number(undefined));  // NaN
console.log(Number([1, 2]));     // NaN (unless array has single numeric element like Number([5]) -> 5)
console.log(Number({a: 1}));     // NaN
```

### Unary `+` operator <a name="unary-plus-conversion"></a>
The unary plus operator (`+`) placed before a value is a concise way to convert it to a number. It works similarly to `Number()`.
```javascript
console.log(+"42");        // 42
console.log(+" -10 ");    // -10 (trims whitespace)
console.log(+true);       // 1
console.log(+false);      // 0
console.log(+"");         // 0
console.log(+"abc");      // NaN
```

### `parseInt()` function <a name="parseint-function"></a>
`parseInt()` parses a string argument and returns an integer of the specified radix (base). It stops parsing when it encounters a character that is not a valid numeral in the specified radix.
```javascript
console.log(parseInt("100px"));    // 100 (stops at 'p')
console.log(parseInt("3.14"));     // 3 (stops at '.')
console.log(parseInt("  20 stars"));// 20 (trims leading space)
console.log(parseInt("0xFF", 16)); // 255 (hexadecimal)
console.log(parseInt("1010", 2));  // 10 (binary)
console.log(parseInt("hello"));    // NaN
console.log(parseInt("12.34.56")); // 12
```
If the string doesn't start with a numeral, `parseInt()` returns `NaN`.

### `parseFloat()` function <a name="parsefloat-function"></a>
`parseFloat()` parses a string argument and returns a floating-point number. It stops parsing when it encounters a character that is not part of a valid floating-point number.
```javascript
console.log(parseFloat("3.14159"));   // 3.14159
console.log(parseFloat("2.5em"));     // 2.5
console.log(parseFloat("  120.75 ")); // 120.75
console.log(parseFloat("PI is 3.14"));// NaN (doesn't start with a number)
console.log(parseFloat("3.14.15"));   // 3.14 (stops at the second dot)
```

### Arithmetic operators (except `+` with strings) <a name="arithmetic-conversion"></a>
When arithmetic operators like `-`, `*`, `/`, `%` are used, JavaScript attempts to convert operands to numbers.
```javascript
console.log("10" - 5);    // 5
console.log("6" * "2");  // 12
console.log("8" / 2);    // 4
console.log(true * 7);  // 7 (true becomes 1)
console.log(false / 3); // 0 (false becomes 0)
```

## 4. Converting to Boolean <a name="to-boolean"></a>

### `Boolean()` function <a name="boolean-function"></a>
The global `Boolean()` function explicitly converts a value to `true` or `false`.
```javascript
console.log(Boolean(1));        // true
console.log(Boolean(0));        // false
console.log(Boolean("hello"));  // true
console.log(Boolean(""));        // false (empty string)
console.log(Boolean(null));     // false
console.log(Boolean(undefined));// false
console.log(Boolean(NaN));      // false
console.log(Boolean({}));       // true (any object is true)
console.log(Boolean([]));       // true (any array is true)
```

### Logical operators (Implicit Coercion) <a name="logical-coercion"></a>
Logical operators (`&&`, `||`, `!`) and conditional contexts (like `if` statements) implicitly convert values to booleans.
```javascript
if ("text") { // "text" is truthy
  console.log("Truthy string");
}

let user = null;
if (!user) { // !null is !false which is true
  console.log("User is not defined (falsy)");
}

console.log(!!"non-empty string"); // true (double negation coerces to boolean)
console.log(!!0);                  // false
```

### Truthy and Falsy Values <a name="truthy-falsy"></a>
In JavaScript, every value has an inherent boolean quality, meaning it can be evaluated as either "truthy" or "falsy" in a boolean context.

**Falsy values** (values that coerce to `false` in boolean contexts):
*   `false`
*   `0` (zero)
*   `-0` (minus zero)
*   `0n` (BigInt zero)
*   `""` (empty string)
*   `null`
*   `undefined`
*   `NaN` (Not a Number)

**Truthy values**: All other values are truthy, including:
*   Any non-empty string (e.g., `"hello"`, `"0"`, `"false"`)
*   Any non-zero number (e.g., `1`, `-1`, `0.5`)
*   Any object (e.g., `{}`, `[]`, `new Date()`)
*   Functions
*   Symbols

```javascript
console.log(Boolean("0"));       // true (string "0" is not empty)
console.log(Boolean("false"));   // true (string "false" is not empty)
console.log(Boolean([]));        // true (empty array is an object)
console.log(Boolean({}));        // true (empty object is an object)
function test() {}
console.log(Boolean(test));     // true (functions are objects)
```

## 5. Type Coercion in Comparisons <a name="coercion-comparisons"></a>

### Loose Equality (`==`) <a name="loose-equality-coercion"></a>
The loose equality operator (`==`) performs type coercion if the operands are of different types. This can lead to non-intuitive results. The rules are complex, but some common cases:
*   Number and String: String is converted to Number.
    `5 == "5"` -> `5 == 5` -> `true`
*   Boolean and Number/String: Boolean is converted to Number (`true` -> 1, `false` -> 0).
    `true == 1` -> `1 == 1` -> `true`
    `false == "0"` -> `0 == "0"` -> `0 == 0` -> `true`
*   `null` and `undefined`: `null == undefined` is `true`. They are not equal to anything else with `==` (unless coerced to number, e.g., `null == 0` is `false`).

**It's generally recommended to use strict equality (`===`) to avoid these coercions.**

### Relational Operators (`<`, `>`, `<=`, `>=`) <a name="relational-coercion"></a>
When comparing values of different types with relational operators, JavaScript usually tries to convert both operands to numbers before comparison.
```javascript
console.log("10" > 5);    // true ( "10" becomes 10)
console.log("2" < "10");  // false (string comparison: "2" comes after "1" lexicographically)
                        // To compare numerically: Number("2") < Number("10") -> true
console.log(true > 0);    // true (true becomes 1)
console.log(null > 0);    // false (null becomes 0, 0 > 0 is false)
console.log(null >= 0);   // true (null becomes 0, 0 >= 0 is true) - a tricky case!
console.log(undefined > 0); // false (undefined becomes NaN, NaN comparisons are false)
```
String comparison is lexicographical (dictionary order). If both operands are strings, they are compared character by character based on their Unicode values.

## 6. Object to Primitive Conversion <a name="object-to-primitive"></a>
When an object is used in a context where a primitive value is expected (e.g., arithmetic operation, `alert()`, comparison), JavaScript tries to convert the object to a primitive.

The conversion process depends on the "hint" provided by the operation:
*   **"string" hint**: Occurs when an operation expects a string (e.g., `alert(obj)`).
*   **"number" hint**: Occurs when an operation expects a number (e.g., `+obj`, `obj - 10`, `obj > anotherObj`).
*   **"default" hint**: Occurs when the operator is unsure of the expected type (e.g., `obj + anotherValue`, `obj == primitive`). For built-in objects, "default" usually behaves like "number" (except for `Date` objects, which treat "default" as "string").

The conversion algorithm is roughly:
1.  If the object has a `Symbol.toPrimitive` method, call it with the hint. If it returns a primitive, use that.
2.  Otherwise, if the hint is "string":
    *   Try calling `obj.toString()`. If it returns a primitive, use it.
    *   Else, try calling `obj.valueOf()`. If it returns a primitive, use it.
3.  Otherwise (hint is "number" or "default"):
    *   Try calling `obj.valueOf()`. If it returns a primitive, use it.
    *   Else, try calling `obj.toString()`. If it returns a primitive, use it.
4.  If neither method returns a primitive, a `TypeError` is thrown.

### The `Symbol.toPrimitive` method <a name="symbol-toprimitive"></a>
This is the most modern way to control object-to-primitive conversion.
```javascript
let user = {
  name: "Alice",
  money: 1000,

  [Symbol.toPrimitive](hint) {
    console.log(`Hint: ${hint}`);
    if (hint === "string") {
      return `User ${this.name}`;
    }
    if (hint === "number") {
      return this.money;
    }
    // hint === "default"
    return this.name + " has " + this.money;
  }
};

console.log(String(user)); // Hint: string -> User Alice
console.log(+user);        // Hint: number -> 1000
console.log(user + 500);   // Hint: default -> Alice has 1000500 (concatenation)
```

### `valueOf()` and `toString()` methods <a name="valueof-tostring-conversion"></a>
*   `valueOf()`: Should return the primitive value of the object. For most objects, it returns the object itself. For wrapper objects like `Number`, `String`, `Boolean`, it returns the wrapped primitive value.
*   `toString()`: Should return a string representation of the object.

```javascript
let obj = {
  value: 42,
  toString() {
    return "I am an object";
  },
  valueOf() {
    return this.value;
  }
};

console.log(String(obj)); // "I am an object" (toString is preferred for string hint)
console.log(Number(obj)); // 42 (valueOf is preferred for number hint)
console.log("Result: " + obj); // "Result: I am an object"
console.log(obj + 10);      // 52 (42 + 10)
```

## 7. Common Pitfalls and Best Practices <a name="pitfalls-best-practices"></a>
*   **Loose Equality (`==`)**: Can lead to confusing results. **Prefer strict equality (`===`)**.
*   **`+` Operator**: Behaves as addition with numbers and concatenation with strings. Be mindful when mixing types.
    `console.log(1 + 2 + "3"); // "33" (1+2=3, then 3+"3"="33")`
    `console.log("1" + 2 + 3); // "123" ("1"+2="12", then "12"+3="123")`
*   **`NaN`**: `NaN` is not equal to anything, including itself (`NaN == NaN` and `NaN === NaN` are both `false`). Use `isNaN()` or `Number.isNaN()` to check for `NaN`.
*   **Relying on Implicit Coercion**: While sometimes convenient, explicit conversions often make code clearer and less error-prone.
*   **`parseInt()` without Radix**: Always specify the radix (usually 10) for `parseInt()` to avoid unexpected behavior with strings starting with "0" (older browsers might interpret as octal).
    `parseInt("010"); // Might be 8 or 10. Better: parseInt("010", 10); // 10`

## 8. Key Takeaways <a name="key-takeaways-type-conversion"></a>
*   Type conversion is the process of changing a value from one data type to another.
*   JavaScript performs implicit conversion (coercion) automatically and allows explicit conversion via functions like `String()`, `Number()`, `Boolean()`.
*   Understanding truthy and falsy values is essential for conditional logic.
*   The `+` operator is overloaded: it performs addition or string concatenation depending on operand types.
*   Arithmetic operators (`-`, `*`, `/`, `%`) generally convert operands to numbers.
*   Loose equality (`==`) involves type coercion; strict equality (`===`) does not and is generally safer.
*   Objects have specific rules for conversion to primitives, customizable via `Symbol.toPrimitive`, `valueOf()`, and `toString()`.
*   Be aware of common pitfalls and prefer explicit conversions for clarity and predictability.

Mastering type conversion will help you write more robust and bug-free JavaScript code.

---

⬅️ [Back to JavaScript Operators](../02-js-operators/README.md) | ➡️ [Next Section: Conditional Statements (if, else, switch)](../04-js-conditional-statements/README.md)
