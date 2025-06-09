# 04. JavaScript Conditional Statements

Conditional statements allow you to execute different blocks of code based on whether a certain condition (or set of conditions) is true or false. They are fundamental for decision-making in programming.

## Table of Contents
1.  [The `if` Statement](#if-statement)
2.  [The `else` Statement](#else-statement)
3.  [The `else if` Statement](#else-if-statement)
4.  [Nested `if` Statements](#nested-if)
5.  [The `switch` Statement](#switch-statement)
    *   [`case`](#switch-case)
    *   [`break`](#switch-break)
    *   [`default`](#switch-default)
    *   [Fall-Through Behavior](#switch-fall-through)
    *   [Grouping Cases](#switch-grouping-cases)
6.  [The Ternary Operator (Conditional Operator)](#ternary-operator-revisit)
7.  [Truthy and Falsy Values in Conditions](#truthy-falsy-conditions)
8.  [Best Practices for Conditional Statements](#best-practices-conditionals)
9.  [Key Takeaways](#key-takeaways-conditionals)

## 1. The `if` Statement <a name="if-statement"></a>
The `if` statement executes a block of code if a specified condition evaluates to `true` (or a truthy value).

**Syntax:**
```javascript
if (condition) {
  // Block of code to be executed if the condition is true
}
```

**Example:**
```javascript
let age = 20;
if (age >= 18) {
  console.log("You are eligible to vote.");
}

let temperature = 30;
if (temperature > 25) {
  console.log("It's a hot day!");
}
```
If the condition is `false` (or a falsy value), the code block inside the `if` statement is skipped.

## 2. The `else` Statement <a name="else-statement"></a>
The `else` statement executes a block of code if the condition in the preceding `if` statement evaluates to `false` (or a falsy value).

**Syntax:**
```javascript
if (condition) {
  // Block of code to be executed if the condition is true
} else {
  // Block of code to be executed if the condition is false
}
```

**Example:**
```javascript
let hour = 14;
if (hour < 12) {
  console.log("Good morning!");
} else {
  console.log("Good afternoon/evening!");
}
// Output: Good afternoon/evening!

let isRaining = false;
if (isRaining) {
  console.log("Take an umbrella.");
} else {
  console.log("Enjoy the sunshine!");
}
// Output: Enjoy the sunshine!
```

## 3. The `else if` Statement <a name="else-if-statement"></a>
The `else if` statement allows you to test multiple conditions in sequence. It executes a block of code if its condition is `true` and all preceding `if` or `else if` conditions were `false`.

**Syntax:**
```javascript
if (condition1) {
  // Block of code for condition1
} else if (condition2) {
  // Block of code for condition2
} else if (condition3) {
  // Block of code for condition3
} else {
  // Block of code if all above conditions are false
}
```

**Example:**
```javascript
let score = 75;

if (score >= 90) {
  console.log("Grade: A");
} else if (score >= 80) {
  console.log("Grade: B");
} else if (score >= 70) {
  console.log("Grade: C");
} else if (score >= 60) {
  console.log("Grade: D");
} else {
  console.log("Grade: F");
}
// Output: Grade: C
```
JavaScript evaluates conditions from top to bottom. Once a condition is met, its corresponding block is executed, and the rest of the `else if` / `else` chain is skipped.

## 4. Nested `if` Statements <a name="nested-if"></a>
You can place `if`, `else if`, or `else` statements inside other conditional statements. This is called nesting.

**Example:**
```javascript
let isLoggedIn = true;
let userRole = "admin";

if (isLoggedIn) {
  console.log("User is logged in.");
  if (userRole === "admin") {
    console.log("Welcome, Admin! You have full access.");
  } else if (userRole === "editor") {
    console.log("Welcome, Editor! You can manage content.");
  } else {
    console.log("Welcome, User! Enjoy your stay.");
  }
} else {
  console.log("Please log in to continue.");
}
// Output:
// User is logged in.
// Welcome, Admin! You have full access.
```
While nesting can be powerful, overly deep nesting can make code hard to read and maintain. Consider refactoring if nesting becomes too complex.

## 5. The `switch` Statement <a name="switch-statement"></a>
The `switch` statement provides an alternative way to handle multiple conditions, especially when comparing a single variable against several possible discrete values.

**Syntax:**
```javascript
switch (expression) {
  case value1:
    // Code to execute if expression === value1
    break; // Important: stops execution from falling through
  case value2:
    // Code to execute if expression === value2
    break;
  // ... more cases
  default:
    // Code to execute if expression matches no cases
}
```

*   The `expression` is evaluated once.
*   Its value is compared with the value of each `case` using strict equality (`===`).
*   If a match is found, the code block associated with that `case` is executed.
*   The `break` statement is crucial. It terminates the `switch` statement. If omitted, execution "falls through" to the next `case` block, regardless of whether its value matches.
*   The `default` case is optional. It executes if no other `case` matches.

**Example:**
```javascript
let dayOfWeek = 3;
let dayName;

switch (dayOfWeek) {
  case 1:
    dayName = "Monday";
    break;
  case 2:
    dayName = "Tuesday";
    break;
  case 3:
    dayName = "Wednesday";
    break;
  case 4:
    dayName = "Thursday";
    break;
  case 5:
    dayName = "Friday";
    break;
  case 6:
    dayName = "Saturday";
    break;
  case 7:
    dayName = "Sunday";
    break;
  default:
    dayName = "Invalid day";
}
console.log(dayName); // Output: Wednesday
```

### Fall-Through Behavior <a name="switch-fall-through"></a>
If `break` is omitted, execution continues into the next `case` block(s) until a `break` is encountered or the `switch` statement ends. This can be intentional for grouping cases.

**Example (Intentional Fall-Through):** <a name="switch-grouping-cases"></a>
```javascript
let fruit = "apple";
let type;

switch (fruit) {
  case "apple":
  case "pear":
  case "banana":
    type = "Common fruit";
    break;
  case "strawberry":
  case "blueberry":
    type = "Berry";
    break;
  default:
    type = "Other type";
}
console.log(`${fruit} is a ${type}.`); // Output: apple is a Common fruit.
```
In this example, if `fruit` is "apple", "pear", or "banana", `type` becomes "Common fruit".

## 6. The Ternary Operator (Conditional Operator) <a name="ternary-operator-revisit"></a>
The ternary operator is a concise way to write simple `if-else` statements, especially for assignments.

**Syntax:**
`condition ? expressionIfTrue : expressionIfFalse`

**Example:**
```javascript
let userAge = 22;
let access = userAge >= 18 ? "Granted" : "Denied";
console.log(`Access: ${access}`); // Output: Access: Granted

let isAuthenticated = false;
let message = isAuthenticated ? "Welcome back!" : "Please log in.";
console.log(message); // Output: Please log in.
```
While compact, avoid using ternary operators for complex logic as it can reduce readability.

## 7. Truthy and Falsy Values in Conditions <a name="truthy-falsy-conditions"></a>
Conditions in `if`, `else if`, and ternary operators don't strictly need to be boolean values. JavaScript will automatically convert non-boolean values to their boolean equivalent (truthy or falsy) to evaluate the condition.

**Falsy values** (evaluate to `false` in a boolean context):
*   `false`
*   `0` (zero)
*   `-0` (minus zero)
*   `0n` (BigInt zero)
*   `""` (empty string)
*   `null`
*   `undefined`
*   `NaN`

All other values are **truthy** (evaluate to `true`).

**Example:**
```javascript
let username = "";
if (username) { // username is "" (falsy)
  console.log(`Welcome, ${username}`);
} else {
  console.log("Please enter your username.");
}
// Output: Please enter your username.

let itemsInCart = 5;
if (itemsInCart) { // itemsInCart is 5 (truthy)
  console.log(`You have ${itemsInCart} items in your cart.`);
}
// Output: You have 5 items in your cart.
```

## 8. Best Practices for Conditional Statements <a name="best-practices-conditionals"></a>
*   **Readability**: Write clear and understandable conditions. Use meaningful variable names.
*   **Braces `{}`**: Always use curly braces for `if`, `else if`, and `else` blocks, even for single-line statements. This improves clarity and prevents potential errors.
    ```javascript
    // Good
    if (condition) {
      doSomething();
    }

    // Avoid (can be error-prone)
    // if (condition) doSomething();
    ```
*   **Avoid Deep Nesting**: Deeply nested `if` statements can be hard to follow. Consider refactoring using functions, `switch` statements, or guard clauses.
    ```javascript
    // Guard clause example
    function processPayment(user) {
      if (!user.isVerified) {
        console.log("User not verified.");
        return;
      }
      // ... proceed with payment
    }
    ```
*   **`switch` vs. `if-else if`**: Use `switch` when comparing a single variable against multiple discrete values. For complex conditions or range checks, `if-else if` is often more suitable.
*   **Strict Equality**: When comparing values, especially in `switch` cases (which use strict equality implicitly), be mindful of types. If you need loose comparison, you might need an `if-else if` structure.
*   **`default` Case**: Include a `default` case in `switch` statements to handle unexpected values and make your code more robust.

## 9. Key Takeaways <a name="key-takeaways-conditionals"></a>
*   Conditional statements (`if`, `else if`, `else`, `switch`) control the flow of execution based on conditions.
*   `if` executes code if a condition is true.
*   `else` executes code if the preceding `if` condition is false.
*   `else if` tests additional conditions if previous ones were false.
*   `switch` is useful for comparing a variable against multiple specific values.
*   The `break` statement is crucial in `switch` cases to prevent fall-through.
*   The ternary operator (`? :`) offers a concise way for simple conditional expressions.
*   JavaScript uses truthy/falsy evaluation for non-boolean conditions.
*   Prioritize readability and maintainability when writing conditional logic.

Conditional statements are essential tools for creating dynamic and responsive JavaScript applications.

---

⬅️ [Back to JavaScript Type Conversion](../03-js-type-conversion/README.md) | ➡️ [Next Section: Loops (for, while, do...while)](../05-js-loops/README.md)
