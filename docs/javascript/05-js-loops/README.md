# 05. JavaScript Loops

Loops are control flow structures that allow you to execute a block of code repeatedly as long as a certain condition is met, or for a specific number of times. They are essential for automating repetitive tasks, iterating over collections of data, and much more.

## Table of Contents
1.  [The `for` Loop](#for-loop)
    *   [Initialization](#for-initialization)
    *   [Condition](#for-condition)
    *   [Final Expression (Increment/Decrement)](#for-final-expression)
2.  [The `while` Loop](#while-loop)
3.  [The `do...while` Loop](#do-while-loop)
4.  [The `for...in` Loop (for Object Properties)](#for-in-loop)
5.  [The `for...of` Loop (for Iterable Objects)](#for-of-loop)
6.  [The `break` Statement](#break-statement)
7.  [The `continue` Statement](#continue-statement)
8.  [Labeled Statements (Advanced)](#labeled-statements)
9.  [Nested Loops](#nested-loops)
10. [Choosing the Right Loop](#choosing-loop)
11. [Best Practices for Loops](#best-practices-loops)
12. [Key Takeaways](#key-takeaways-loops)

## 1. The `for` Loop <a name="for-loop"></a>
The `for` loop is often used when you know in advance how many times you want to execute a block of code.

**Syntax:**
```javascript
for (initialization; condition; finalExpression) {
  // Code block to be executed
}
```

*   **Initialization <a name="for-initialization"></a>**: Executed once before the loop starts. Typically used to declare and initialize a loop counter variable (e.g., `let i = 0`).
*   **Condition <a name="for-condition"></a>**: Evaluated before each iteration. If it's `true` (or truthy), the loop body executes. If `false` (or falsy), the loop terminates.
*   **Final Expression <a name="for-final-expression"></a>**: Executed at the end of each iteration, after the loop body. Usually used to increment or decrement the loop counter (e.g., `i++`, `i--`).

**Example:**
```javascript
// Print numbers from 0 to 4
for (let i = 0; i < 5; i++) {
  console.log(i);
}
// Output:
// 0
// 1
// 2
// 3
// 4

// Iterate over an array
let colors = ["red", "green", "blue"];
for (let i = 0; i < colors.length; i++) {
  console.log(colors[i]);
}
// Output:
// red
// green
// blue
```
All three parts of the `for` loop are optional. For example, `for (;;)` creates an infinite loop.

## 2. The `while` Loop <a name="while-loop"></a>
The `while` loop executes a block of code as long as a specified condition is `true` (or truthy). The condition is checked *before* each iteration.

**Syntax:**
```javascript
while (condition) {
  // Code block to be executed
  // Make sure to include logic that eventually makes the condition false
  // to avoid an infinite loop (e.g., incrementing a counter)
}
```

**Example:**
```javascript
let count = 0;
while (count < 3) {
  console.log(`Count is: ${count}`);
  count++;
}
// Output:
// Count is: 0
// Count is: 1
// Count is: 2

let i = 5;
while (i > 0) {
  console.log(i);
  i--;
}
// Output:
// 5
// 4
// 3
// 2
// 1
```
If the condition is initially `false`, the `while` loop's body will never execute.

## 3. The `do...while` Loop <a name="do-while-loop"></a>
The `do...while` loop is similar to the `while` loop, but the condition is checked *after* the loop body executes. This means the loop body will always execute at least once, even if the condition is initially `false`.

**Syntax:**
```javascript
do {
  // Code block to be executed
  // Logic to eventually make the condition false
} while (condition);
```

**Example:**
```javascript
let num = 0;
do {
  console.log(`Number is: ${num}`);
  num++;
} while (num < 3);
// Output:
// Number is: 0
// Number is: 1
// Number is: 2

let attempts = 3;
do {
  console.log(`Attempting connection... remaining: ${attempts}`);
  attempts--;
} while (attempts > 3); // Condition is initially false
// Output:
// Attempting connection... remaining: 3 (executes once)
```

## 4. The `for...in` Loop (for Object Properties) <a name="for-in-loop"></a>
The `for...in` loop iterates over the enumerable properties of an object (including inherited enumerable properties).

**Syntax:**
```javascript
for (let key in object) {
  // Code block to be executed for each property
  // 'key' will be the property name (string)
  // 'object[key]' will be the property value
}
```

**Example:**
```javascript
let person = {
  name: "Alice",
  age: 30,
  city: "New York"
};

for (let propName in person) {
  console.log(`${propName}: ${person[propName]}`);
}
// Output (order may vary):
// name: Alice
// age: 30
// city: New York

// Using hasOwnProperty to iterate only over own properties
for (let key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(`Own property - ${key}: ${person[key]}`);
  }
}
```
**Caution**: `for...in` is generally not recommended for iterating over arrays because:
*   It iterates over array indices as strings (e.g., "0", "1", "2").
*   It can iterate over non-element properties if the array object has them.
*   The order of iteration is not guaranteed for object properties (though modern engines often follow insertion order for non-integer keys).
Use `for` loops, `Array.prototype.forEach()`, or `for...of` for arrays.

## 5. The `for...of` Loop (for Iterable Objects) <a name="for-of-loop"></a>
The `for...of` loop (introduced in ES6) iterates over the values of iterable objects, such as Arrays, Strings, Maps, Sets, NodeLists, etc.

**Syntax:**
```javascript
for (let value of iterable) {
  // Code block to be executed for each value
}
```

**Example (Array):**
```javascript
let fruits = ["apple", "banana", "cherry"];
for (let fruit of fruits) {
  console.log(fruit);
}
// Output:
// apple
// banana
// cherry
```

**Example (String):**
```javascript
let message = "Hello";
for (let char of message) {
  console.log(char);
}
// Output:
// H
// e
// l
// l
// o
```

**Example (Map):**
```javascript
let myMap = new Map();
myMap.set("a", 1);
myMap.set("b", 2);

for (let [key, value] of myMap) {
  console.log(`${key} -> ${value}`);
}
// Output:
// a -> 1
// b -> 2
```
`for...of` cannot be used to iterate over plain objects because they are not iterable by default. You would need to use `for...in` or methods like `Object.keys()`, `Object.values()`, or `Object.entries()` with `for...of`.

```javascript
let car = { make: "Toyota", model: "Camry" };

// Iterate over keys
for (let key of Object.keys(car)) {
  console.log(key); // make, model
}

// Iterate over values
for (let value of Object.values(car)) {
  console.log(value); // Toyota, Camry
}

// Iterate over key-value pairs
for (let [key, value] of Object.entries(car)) {
  console.log(`${key}: ${value}`); // make: Toyota, model: Camry
}
```

## 6. The `break` Statement <a name="break-statement"></a>
The `break` statement is used to terminate the current loop (or `switch` statement) prematurely. Execution resumes at the statement immediately following the loop.

**Example:**
```javascript
for (let i = 0; i < 10; i++) {
  if (i === 5) {
    console.log("Breaking at 5");
    break; // Exit the loop when i is 5
  }
  console.log(i);
}
// Output:
// 0
// 1
// 2
// 3
// 4
// Breaking at 5
```

## 7. The `continue` Statement <a name="continue-statement"></a>
The `continue` statement skips the rest of the current iteration of a loop and proceeds to the next iteration.

**Example (skip even numbers):**
```javascript
for (let i = 0; i < 10; i++) {
  if (i % 2 === 0) {
    continue; // Skip the rest of this iteration if i is even
  }
  console.log(i); // This line only executes for odd numbers
}
// Output:
// 1
// 3
// 5
// 7
// 9
```

## 8. Labeled Statements (Advanced) <a name="labeled-statements"></a>
Labels can be used with `break` and `continue` to control the flow of nested loops more precisely. A label is an identifier followed by a colon (`:`).

**Syntax:**
```javascript
outerLoop: // Label for the outer loop
for (let i = 0; i < 3; i++) {
  innerLoop: // Label for the inner loop
  for (let j = 0; j < 3; j++) {
    if (i === 1 && j === 1) {
      // break outerLoop; // Breaks out of both loops
      continue outerLoop; // Skips to the next iteration of outerLoop
    }
    console.log(`i=${i}, j=${j}`);
  }
}
```
When `continue outerLoop` is executed, it skips the rest of `innerLoop` and `outerLoop`'s current iteration, proceeding to `outerLoop`'s next iteration.
If `break outerLoop` were used, both loops would terminate.

Labels are not commonly used as they can make code harder to follow. Consider refactoring into functions if you find yourself needing them.

## 9. Nested Loops <a name="nested-loops"></a>
You can place one loop inside another. This is common for working with multi-dimensional data or generating combinations.

**Example (generating pairs):**
```javascript
for (let i = 1; i <= 2; i++) {
  for (let j = 1; j <= 3; j++) {
    console.log(`(${i}, ${j})`);
  }
}
// Output:
// (1, 1)
// (1, 2)
// (1, 3)
// (2, 1)
// (2, 2)
// (2, 3)
```
Be mindful of performance with nested loops, especially with large datasets, as the complexity can increase significantly.

## 10. Choosing the Right Loop <a name="choosing-loop"></a>
*   **`for` loop**: Best when the number of iterations is known beforehand (e.g., iterating a fixed number of times or over array indices).
*   **`while` loop**: Suitable when the number of iterations is unknown and depends on a condition being met. Condition is checked before iteration.
*   **`do...while` loop**: Similar to `while`, but guarantees at least one execution of the loop body. Condition is checked after iteration.
*   **`for...in` loop**: Use for iterating over the enumerable properties of an object. Not ideal for arrays.
*   **`for...of` loop**: The modern, preferred way to iterate over the values of iterable objects (Arrays, Strings, Maps, Sets, etc.).
*   Array methods like `forEach()`, `map()`, `filter()`, `reduce()` are often more concise and expressive for array manipulation than traditional loops.

## 11. Best Practices for Loops <a name="best-practices-loops"></a>
*   **Avoid Infinite Loops**: Ensure your loop condition will eventually become `false`.
*   **Clarity**: Make loop conditions and bodies easy to understand.
*   **Scope**: Declare loop variables with `let` (or `const` in `for...of`) to keep them block-scoped.
*   **Efficiency**: Be mindful of performance, especially with nested loops or operations inside loops that run many times.
*   **`for...of` over `for...in` for Arrays**: For iterating over array elements, `for...of` is safer and more direct than `for...in`.
*   **Cache Array Length**: In performance-critical `for` loops iterating over arrays, caching `array.length` in a variable can offer a micro-optimization if the length doesn't change within the loop (though modern JS engines are very good at optimizing this).
    ```javascript
    // for (let i = 0, len = myArray.length; i < len; i++) { ... }
    ```

## 12. Key Takeaways <a name="key-takeaways-loops"></a>
*   Loops (`for`, `while`, `do...while`, `for...in`, `for...of`) provide mechanisms for code repetition.
*   `for` is for known iteration counts; `while` and `do...while` for condition-based iteration.
*   `for...in` iterates object property names; `for...of` iterates values of iterables.
*   `break` exits a loop; `continue` skips to the next iteration.
*   Choose the loop type that best fits the task for clarity and efficiency.
*   Be cautious of infinite loops and performance implications of nested loops.

Loops are a cornerstone of programming, enabling powerful automation and data processing capabilities.

---

⬅️ [Back to JavaScript Conditional Statements](../04-js-conditional-statements/README.md) | ➡️ [Next Section: Functions](../06-js-functions/README.md)
