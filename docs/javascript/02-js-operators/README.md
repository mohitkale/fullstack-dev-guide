# 02. JavaScript Operators

Operators in JavaScript are special symbols or keywords used to perform operations on values and variables (operands). They are the building blocks for expressions, allowing you to manipulate data, make decisions, and control the flow of your programs.

## Table of Contents
1.  [What are Operands and Operators?](#operands-operators)
2.  [Assignment Operators](#assignment-operators-js)
3.  [Arithmetic Operators](#arithmetic-operators-js)
4.  [Comparison Operators](#comparison-operators-js)
    *   [Equality (`==`) vs. Strict Equality (`===`)](#equality-vs-strict)
5.  [Logical Operators](#logical-operators-js)
    *   [Short-Circuit Evaluation](#short-circuiting)
6.  [Bitwise Operators](#bitwise-operators-js)
7.  [String Operators](#string-operators-js)
8.  [Conditional (Ternary) Operator](#ternary-operator-js)
9.  [Unary Operators](#unary-operators-js)
    *   [`typeof`](#typeof-operator-detail)
    *   [`delete`](#delete-operator)
    *   [`void`](#void-operator)
10. [Relational Operators](#relational-operators-js)
    *   [`in`](#in-operator)
    *   [`instanceof`](#instanceof-operator)
11. [Comma Operator](#comma-operator-js)
12. [Operator Precedence and Associativity](#operator-precedence)
13. [Key Takeaways](#key-takeaways-operators)

## 1. What are Operands and Operators? <a name="operands-operators"></a>
*   **Operands**: The values or variables that an operator works on. For example, in `x + y`, `x` and `y` are operands.
*   **Operators**: The symbol that performs the operation. In `x + y`, `+` is the operator.

JavaScript operators can be classified by the number of operands they take:
*   **Unary operators**: Work with one operand (e.g., `x++`, `typeof x`).
*   **Binary operators**: Work with two operands (e.g., `x + y`, `x > y`).
*   **Ternary operator**: Works with three operands (the conditional operator `condition ? exprIfTrue : exprIfFalse`).

## 2. Assignment Operators <a name="assignment-operators-js"></a>
Assignment operators are used to assign values to variables.

| Operator | Example      | Same As         | Description                      |
|----------|--------------|-----------------|----------------------------------|
| `=`      | `x = y`      | `x = y`         | Assigns value of `y` to `x`      |
| `+=`     | `x += y`     | `x = x + y`     | Adds `y` to `x`, assigns result  |
| `-=`     | `x -= y`     | `x = x - y`     | Subtracts `y` from `x`, assigns  |
| `*=`     | `x *= y`     | `x = x * y`     | Multiplies `x` by `y`, assigns   |
| `/=`     | `x /= y`     | `x = x / y`     | Divides `x` by `y`, assigns      |
| `%=`     | `x %= y`     | `x = x % y`     | Modulo of `x` by `y`, assigns    |
| `**=`    | `x **= y`    | `x = x ** y`    | Exponentiation, assigns result   |

```javascript
let x = 10;
let y = 5;

x += y; // x is now 15 (10 + 5)
console.log(x); // 15

x *= 2; // x is now 30 (15 * 2)
console.log(x); // 30
```

## 3. Arithmetic Operators <a name="arithmetic-operators-js"></a>
Arithmetic operators perform mathematical calculations.

| Operator | Name             | Example    | Description                                   |
|----------|------------------|------------|-----------------------------------------------|
| `+`      | Addition         | `x + y`    | Adds `x` and `y` (also string concatenation)  |
| `-`      | Subtraction      | `x - y`    | Subtracts `y` from `x`                        |
| `*`      | Multiplication   | `x * y`    | Multiplies `x` by `y`                         |
| `/`      | Division         | `x / y`    | Divides `x` by `y`                            |
| `%`      | Modulus (Remainder)| `x % y`    | Returns the remainder of `x` divided by `y`   |
| `**`     | Exponentiation   | `x ** y`   | Raises `x` to the power of `y` (ES2016)       |
| `++`     | Increment        | `x++` or `++x` | Increments `x` by 1 (postfix or prefix)   |
| `--`     | Decrement        | `x--` or `--x` | Decrements `x` by 1 (postfix or prefix)   |

```javascript
let a = 10;
let b = 3;

console.log(a + b); // 13
console.log(a - b); // 7
console.log(a * b); // 30
console.log(a / b); // 3.333...
console.log(a % b); // 1 (remainder of 10 / 3)
console.log(a ** b); // 1000 (10 to the power of 3)

let c = 5;
console.log(c++); // 5 (postfix: returns original value, then increments)
console.log(c);   // 6

let d = 5;
console.log(++d); // 6 (prefix: increments, then returns new value)
console.log(d);   // 6
```

## 4. Comparison Operators <a name="comparison-operators-js"></a>
Comparison operators compare two operands and return a boolean value (`true` or `false`).

| Operator | Name                     | Example    | Description                                       |
|----------|--------------------------|------------|---------------------------------------------------|
| `==`     | Equal to (Loose Equality)| `x == y`   | Returns `true` if `x` is equal to `y` (type coercion) |
| `!=`     | Not equal to             | `x != y`   | Returns `true` if `x` is not equal to `y` (type coercion) |
| `===`    | Strictly equal to        | `x === y`  | Returns `true` if `x` is equal to `y` AND same type |
| `!==`    | Strictly not equal to    | `x !== y`  | Returns `true` if `x` is not equal to `y` OR not same type |
| `>`      | Greater than             | `x > y`    | Returns `true` if `x` is greater than `y`         |
| `<`      | Less than                | `x < y`    | Returns `true` if `x` is less than `y`            |
| `>=`     | Greater than or equal to | `x >= y`   | Returns `true` if `x` is greater than or equal to `y` |
| `<=`     | Less than or equal to    | `x <= y`   | Returns `true` if `x` is less than or equal to `y`  |

### Equality (`==`) vs. Strict Equality (`===`) <a name="equality-vs-strict"></a>
*   **Loose Equality (`==`)**: Performs type coercion if the operands are of different types before comparison. This can lead to unexpected results.
    ```javascript
    console.log(5 == "5");    // true (string "5" is coerced to number 5)
    console.log(0 == false);  // true (false is coerced to 0)
    console.log(null == undefined); // true
    ```
*   **Strict Equality (`===`)**: Compares both value and type, without type coercion. It's generally safer and recommended.
    ```javascript
    console.log(5 === "5");   // false (number vs. string)
    console.log(0 === false); // false (number vs. boolean)
    console.log(null === undefined); // false
    ```
**Best Practice**: Almost always use `===` and `!==` for comparisons to avoid subtle bugs related to type coercion.

## 5. Logical Operators <a name="logical-operators-js"></a>
Logical operators are typically used with boolean values. They can also return one of the original operand's values (due to short-circuiting).

| Operator | Name          | Example         | Description                                                      |
|----------|---------------|-----------------|------------------------------------------------------------------|
| `&&`     | Logical AND   | `expr1 && expr2`| Returns `expr1` if it can be converted to `false`; otherwise, returns `expr2`. |
| `||`     | Logical OR    | `expr1 || expr2`| Returns `expr1` if it can be converted to `true`; otherwise, returns `expr2`.  |
| `!`      | Logical NOT   | `!expr`         | Returns `false` if `expr` can be converted to `true`; otherwise, returns `true`. |

```javascript
let isAdult = true;
let hasLicense = false;

console.log(isAdult && hasLicense); // false (true && false)
console.log(isAdult || hasLicense); // true  (true || false)
console.log(!isAdult);              // false (not true)

// Examples with non-boolean values (truthy/falsy)
console.log("Hello" && 0);       // 0 ( "Hello" is truthy, so it returns 0)
console.log(null && "World");    // null (null is falsy)
console.log("" || "Default");     // "Default" ("" is falsy)
console.log(10 || "Ignored");    // 10 (10 is truthy)
```

### Short-Circuit Evaluation <a name="short-circuiting"></a>
Logical operators `&&` and `||` use short-circuit evaluation:
*   For `expr1 && expr2`: If `expr1` is falsy, `expr2` is not evaluated.
*   For `expr1 || expr2`: If `expr1` is truthy, `expr2` is not evaluated.

This is useful for conditional execution or setting default values:
```javascript
let user = null;
let displayName = user && user.name; // user is null (falsy), so user.name is not accessed. displayName is null.
console.log(displayName); // null

let score = 0;
let result = score || "No score yet"; // score is 0 (falsy), result is "No score yet"
console.log(result); // "No score yet"
```

## 6. Bitwise Operators <a name="bitwise-operators-js"></a>
Bitwise operators treat their operands as a sequence of 32 bits (zeros and ones) and perform operations on their binary representations. These are less commonly used in everyday web development but can be useful in specific scenarios (e.g., graphics, low-level operations).

| Operator | Name                 | Example       | Description                                  |
|----------|----------------------|---------------|----------------------------------------------|
| `&`      | Bitwise AND          | `x & y`       | Returns 1 if both bits are 1, else 0         |
| `|`      | Bitwise OR           | `x | y`       | Returns 1 if either bit is 1, else 0         |
| `^`      | Bitwise XOR          | `x ^ y`       | Returns 1 if bits are different, else 0      |
| `~`      | Bitwise NOT          | `~x`          | Inverts all bits of `x`                      |
| `<<`     | Left Shift           | `x << y`      | Shifts bits of `x` left by `y` positions     |
| `>>`     | Sign-propagating Right Shift | `x >> y` | Shifts bits of `x` right by `y` positions (preserves sign) |
| `>>>`    | Zero-fill Right Shift| `x >>> y`     | Shifts bits of `x` right by `y` positions (fills with 0) |

```javascript
console.log(5 & 1);  // Binary: 0101 & 0001 = 0001 (Decimal: 1)
console.log(5 | 1);  // Binary: 0101 | 0001 = 0101 (Decimal: 5)
console.log(5 ^ 1);  // Binary: 0101 ^ 0001 = 0100 (Decimal: 4)
console.log(~5);     // Inverts bits of 5 (results in -6 due to two's complement representation)
console.log(5 << 1); // Binary: 0101 << 1 = 1010 (Decimal: 10)
console.log(5 >> 1); // Binary: 0101 >> 1 = 0010 (Decimal: 2)
console.log(-5 >>> 1); // Shifts with zero fill, result depends on bit length
```

## 7. String Operators <a name="string-operators-js"></a>
The primary string operator is `+` for concatenation.

```javascript
let firstName = "John";
let lastName = "Doe";
let fullName = firstName + " " + lastName; // "John Doe"
console.log(fullName);

let str = "Result: ";
str += 10; // "Result: 10" (number 10 is coerced to string "10")
console.log(str);
```
Template literals (backticks `` `...` ``) provide a more convenient way for string interpolation and multi-line strings, often preferred over the `+` operator for complex string construction.

## 8. Conditional (Ternary) Operator <a name="ternary-operator-js"></a>
The conditional operator is the only JavaScript operator that takes three operands. It's a shorthand for an `if-else` statement.

Syntax: `condition ? expressionIfTrue : expressionIfFalse`

```javascript
let age = 20;
let canVote = (age >= 18) ? "Yes, can vote" : "No, cannot vote";
console.log(canVote); // "Yes, can vote"

let isAuthenticated = true;
let greeting = "Welcome, " + (isAuthenticated ? "User" : "Guest");
console.log(greeting); // "Welcome, User"
```

## 9. Unary Operators <a name="unary-operators-js"></a>
Unary operators work on a single operand.

*   **Unary Plus (`+`)**: Tries to convert an operand into a number.
    ```javascript
    console.log(+"5");    // 5 (number)
    console.log(+true);   // 1
    console.log(+null);   // 0
    ```
*   **Unary Negation (`-`)**: Tries to convert an operand into a number and negates it.
    ```javascript
    console.log(-"5");   // -5
    console.log(-false);  // -0
    ```
*   **Increment (`++`)** and **Decrement (`--`)**: Already covered in Arithmetic Operators.
*   **Logical NOT (`!`)**: Already covered in Logical Operators.
*   **`typeof`**: Returns a string indicating the type of an unevaluated operand.
    ```javascript
    console.log(typeof 42);       // "number"
    console.log(typeof "hello");  // "string"
    ```
*   **`delete`**: Deletes an object's property or an element at a specific index in an array. Returns `true` if successful or if the property doesn't exist; `false` if the property is non-configurable.
    ```javascript
    let myObj = { name: "Alice", age: 30 };
    delete myObj.age;
    console.log(myObj); // { name: "Alice" }

    let myArray = [1, 2, 3];
    delete myArray[1];
    console.log(myArray); // [1, empty, 3] (leaves a hole, doesn't re-index)
    ```
*   **`void`**: Evaluates an expression and returns `undefined`. Often used to obtain the `undefined` primitive value safely or to prevent an expression from returning a value where it's not desired (e.g., `javascript:void(0)` in links).
    ```javascript
    console.log(void(0)); // undefined
    let x = void(document.body.style.backgroundColor = 'blue');
    console.log(x); // undefined (background color changes, but x is undefined)
    ```

## 10. Relational Operators <a name="relational-operators-js"></a>

### `in` Operator <a name="in-operator"></a>
Returns `true` if the specified property is in the specified object or its prototype chain.
```javascript
let car = { make: "Toyota", model: "Camry" };
console.log("make" in car);      // true
console.log("year" in car);      // false
console.log("toString" in car); // true (inherited from Object.prototype)

let arr = ["a", "b", "c"];
console.log(0 in arr);     // true (index 0 exists)
console.log(3 in arr);     // false (index 3 does not exist)
```

### `instanceof` Operator <a name="instanceof-operator"></a>
Returns `true` if the specified object is an instance of the specified constructor or a constructor in its prototype chain.
```javascript
function Person(name) {
  this.name = name;
}
let person1 = new Person("Alice");

console.log(person1 instanceof Person);  // true
console.log(person1 instanceof Object);  // true (Person inherits from Object)

let today = new Date();
console.log(today instanceof Date);    // true

let names = ["A", "B"];
console.log(names instanceof Array);   // true
console.log(names instanceof Object);  // true
```

## 11. Comma Operator <a name="comma-operator-js"></a>
The comma operator (`,`) evaluates each of its operands (from left to right) and returns the value of the last operand. It's often used in `for` loops to include multiple expressions in the initialization or update part.

```javascript
let x = (1, 2, 3); // x will be 3
console.log(x);

for (let i = 0, j = 9; i <= j; i++, j--) {
  console.log(`i=${i}, j=${j}`);
}
// Output:
// i=0, j=9
// i=1, j=8
// ...
// i=4, j=5
```

## 12. Operator Precedence and Associativity <a name="operator-precedence"></a>
*   **Operator Precedence**: Determines the order in which operators are evaluated in an expression with multiple operators. For example, multiplication (`*`) has higher precedence than addition (`+`).
    ```javascript
    let result = 3 + 4 * 5; // 4 * 5 is evaluated first, so result is 3 + 20 = 23
    ```
*   **Associativity**: Determines the order in which operators of the same precedence are evaluated (left-to-right or right-to-left).
    *   Most binary operators are left-associative (e.g., `a - b - c` is `(a - b) - c`).
    *   Assignment operators and the conditional operator are right-associative (e.g., `a = b = c` is `a = (b = c)`).

Parentheses `()` can be used to override the default precedence and explicitly control the order of evaluation.
```javascript
let resultWithParen = (3 + 4) * 5; // (3 + 4) is evaluated first, so result is 7 * 5 = 35
```
Refer to MDN's Operator Precedence table for a complete list.

## 13. Key Takeaways <a name="key-takeaways-operators"></a>
*   Operators are symbols that perform operations on operands (values/variables).
*   JavaScript offers a wide range of operators: assignment, arithmetic, comparison, logical, bitwise, string, conditional, unary, and relational.
*   Always prefer strict equality (`===` and `!==`) over loose equality (`==` and `!=`).
*   Logical operators (`&&`, `||`) use short-circuit evaluation, which can be leveraged for concise conditional logic.
*   Understanding operator precedence and associativity is crucial for writing correct expressions. Use parentheses `()` to clarify or override default order.
*   The `typeof` operator helps identify data types, while `instanceof` checks an object's prototype chain.

Operators are fundamental for performing calculations, making decisions, and controlling the logic of your JavaScript programs.

---

⬅️ [Back to JavaScript Variables and Data Types](../01-js-variables-datatypes/README.md) | ➡️ [Next Section: Type Conversion](../03-js-type-conversion/README.md)
