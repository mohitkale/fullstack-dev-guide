# 05. TypeScript Functions

Functions are fundamental building blocks in JavaScript and TypeScript. TypeScript enhances JavaScript functions by allowing you to specify types for parameters and return values, providing better tooling and error checking during development.

This tutorial covers various aspects of working with functions in TypeScript.

## Table of Contents
1.  [Introduction to Functions in TypeScript](#intro-to-functions-ts)
2.  [Typing Functions](#typing-functions)
    *   [Parameter Type Annotations](#parameter-types)
    *   [Return Type Annotations](#return-types)
    *   [Anonymous Functions and Contextual Typing](#anonymous-functions)
3.  [Function Types](#function-types-declarations)
    *   [Writing the Function Type](#writing-function-type)
    *   [Inferring the Types](#inferring-types)
4.  [Optional and Default Parameters](#optional-default-params)
    *   [Optional Parameters](#optional-params)
    *   [Default-Initialized Parameters](#default-params)
5.  [Rest Parameters](#rest-params)
6.  [Typing `this`](#typing-this)
    *   [`this` and Arrow Functions](#this-arrow-functions)
    *   [`this` Parameters in Functions](#this-parameters)
    *   [`this` Parameters in Callbacks](#this-in-callbacks)
7.  [Overloads](#overloads)
8.  [Key Takeaways](#key-takeaways-functions)

## 1. Introduction to Functions in TypeScript <a name="intro-to-functions-ts"></a>
Functions in TypeScript can be defined in several ways, similar to JavaScript (function declarations, function expressions). TypeScript adds a layer of type safety on top of these.

```typescript
// Named function (Function Declaration)
function add(x: number, y: number): number {
  return x + y;
}

// Anonymous function (Function Expression)
let multiply = function(x: number, y: number): number {
  return x * y;
};

// Arrow function expression
let subtract = (x: number, y: number): number => x - y;
```

## 2. Typing Functions <a name="typing-functions"></a>

### Parameter Type Annotations <a name="parameter-types"></a>
You can add type annotations after each parameter name to declare the type it expects.

```typescript
function greet(name: string, age: number) {
  console.log(`Hello, ${name}! You are ${age} years old.`);
}

greet("Alice", 30);
// greet("Bob", "forty"); // Error: Argument of type 'string' is not assignable to parameter of type 'number'.
```
If no type annotation is provided, TypeScript will try to infer it or default to `any` if `noImplicitAny` is not set.

### Return Type Annotations <a name="return-types"></a>
You can also annotate the return type of a function. This is done after the parameter list.

```typescript
function getFavoriteNumber(): number {
  return 26;
  // return "twenty-six"; // Error: Type 'string' is not assignable to type 'number'.
}
```
TypeScript can usually infer the return type of a function based on its `return` statements, but explicit annotations are good for clarity and ensuring the function returns what you expect.

For functions that don't return a value, you can use `void` as the return type:
```typescript
function logMessage(message: string): void {
  console.log(message);
  // return message; // Error: Type 'string' is not assignable to type 'void'.
}
```

### Anonymous Functions and Contextual Typing <a name="anonymous-functions"></a>
When an anonymous function is assigned to a variable with a type, or passed as an argument to a function that expects a certain function type, TypeScript can infer the parameter types and return type. This is known as contextual typing.

```typescript
const names = ["Alice", "Bob", "Charlie"];

// Contextual typing for 's' parameter and inferred return type
names.forEach(function (s) {
  // console.log(s.toUppercase()); // Error: Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
  console.log(s.toUpperCase());
});

// Arrow function example
names.forEach((s) => {
  console.log(s.toLowerCase());
});
```

## 3. Function Types <a name="function-types-declarations"></a>
You can define a type that describes a function's signature (parameters and return type).

### Writing the Function Type <a name="writing-function-type"></a>
```typescript
let myAdd: (baseValue: number, increment: number) => number;

myAdd = function(x: number, y: number): number {
  return x + y;
};

// myAdd = function(x: string, y: string): string { return x + y; }; // Error: Type '(x: string, y: string) => string' is not assignable to type '(baseValue: number, increment: number) => number'.

console.log(myAdd(10, 5)); // 15
```
The parameter names in the function type (`baseValue`, `increment`) are just for readability and don't need to match the names in the actual function implementation (`x`, `y`). Only the types matter.

### Inferring the Types <a name="inferring-types"></a>
When assigning a function to a variable with a function type, the types of the parameters and the return type can often be inferred if not explicitly stated in the function expression.

```typescript
let combineStrings: (a: string, b: string) => string;

// Parameters 'first' and 'second' and return type are inferred as string
combineStrings = (first, second) => {
  return first + second;
};

console.log(combineStrings("Hello, ", "TypeScript")); // "Hello, TypeScript"
```

## 4. Optional and Default Parameters <a name="optional-default-params"></a>

### Optional Parameters <a name="optional-params"></a>
In TypeScript, you can mark parameters as optional by appending a `?` to their name. Optional parameters must come after required parameters.

```typescript
function buildName(firstName: string, lastName?: string): string {
  if (lastName) {
    return firstName + " " + lastName;
  }
  return firstName;
}

let result1 = buildName("Bob"); // "Bob"
let result2 = buildName("Bob", "Adams"); // "Bob Adams"
// let result3 = buildName("Bob", "Adams", "Sr."); // Error: Expected 1-2 arguments, but got 3.

console.log(result1);
console.log(result2);
```
Inside the function, an optional parameter will have the value `undefined` if not provided.

### Default-Initialized Parameters <a name="default-params"></a>
You can also set a default value for a parameter. If a value is not provided for a default-initialized parameter, or if `undefined` is passed, it will take its default value.

```typescript
function buildFullName(firstName: string, lastName: string = "Smith"): string {
  return firstName + " " + lastName;
}

let name1 = buildFullName("John"); // "John Smith"
let name2 = buildFullName("John", "Doe"); // "John Doe"
let name3 = buildFullName("John", undefined); // "John Smith" (undefined is passed, so default is used)

console.log(name1);
console.log(name2);
console.log(name3);
```
Default-initialized parameters don't need to appear after required parameters. However, if they do, callers must explicitly pass `undefined` to get the default value if they want to skip that parameter.

```typescript
function greetPrefix(prefix: string = "Mr.", name: string): string {
  return `${prefix} ${name}`;
}

// greetPrefix("Alice"); // Error: Expected 2 arguments, but got 1.
console.log(greetPrefix(undefined, "Alice")); // "Mr. Alice"
console.log(greetPrefix("Dr.", "Who")); // "Dr. Who"
```

## 5. Rest Parameters <a name="rest-params"></a>
Rest parameters allow a function to accept an indefinite number of arguments as an array. They are denoted by `...` before the parameter name and must be the last parameter in the function signature. The type of a rest parameter is an array type.

```typescript
function buildNameWithFriends(firstName: string, ...restOfName: string[]): string {
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildNameWithFriends("Joseph", "Samuel", "Lucas", "MacKinzie");
console.log(employeeName); // "Joseph Samuel Lucas MacKinzie"

function sumNumbers(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sumNumbers(1, 2, 3)); // 6
console.log(sumNumbers(10, 20, 30, 40, 50)); // 150
```

## 6. Typing `this` <a name="typing-this"></a>
The behavior of `this` in JavaScript can be tricky. TypeScript helps by allowing you to specify the type of `this` within a function.

### `this` and Arrow Functions <a name="this-arrow-functions"></a>
Arrow functions capture the `this` value of the enclosing context where they are created, rather than having their own `this` binding. This is often desirable.

```typescript
let deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  createCardPicker: function() {
    // Arrow function captures 'this' from createCardPicker (which is 'deck')
    return () => {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);
      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  }
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();
console.log("card: " + pickedCard.card + " of " + pickedCard.suit);
```

### `this` Parameters in Functions <a name="this-parameters"></a>
If a regular function (not an arrow function) uses `this`, you can provide an explicit `this` parameter in its signature. This parameter is fake; it's only used for type checking and is erased during compilation. It must be the first parameter.

```typescript
interface Card {
  suit: string;
  card: number;
}
interface Deck {
  suits: string[];
  cards: number[];
  createCardPicker(this: Deck): () => Card; // 'this' parameter explicitly typed
}

let myDeck: Deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  createCardPicker: function(this: Deck) { // 'this' is now typed as Deck
    return () => {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);
      // 'this' here refers to the 'this' of createCardPicker, which is Deck
      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  }
};

let myCardPicker = myDeck.createCardPicker();
let myPickedCard = myCardPicker();
console.log("My card: " + myPickedCard.card + " of " + myPickedCard.suit);
```
This helps prevent errors when `this` might be unintentionally rebound.

### `this` Parameters in Callbacks <a name="this-in-callbacks"></a>
You can also use `this` parameters to type callbacks where `this` is expected to be a certain type.

```typescript
interface UIElement {
  addClickListener(onclick: (this: void, e: Event) => void): void;
}

class Button {
  label: string = "Click Me";
  addClickListener(onclick: (this: void, e: Event) => void): void {
    // Attach event listener, ensuring 'this' in callback is not the button instance
    // For example, using an arrow function or .bind(null)
    document.addEventListener('click', (e) => onclick(e)); 
  }
}

let uiButton = new Button();
uiButton.addClickListener(function(e) {
  // this.label; // Error: 'this' implicitly has type 'any' because it does not have a type annotation.
                 // If you specified `this: void`, then `this` is unusable here.
  console.log("Clicked! Event: ", e.type);
});

// If you want 'this' to be the button instance in the callback:
interface UIElementWithThis {
    addClickListener(onclick: (this: this, e: Event) => void): void;
}
class Handler {
    info: string = "Handled!";
    onClick(this: Handler, e: Event) {
        // 'this' is Handler here
        console.log(this.info, e.type);
    }
}
let h = new Handler();
// uiButton.addClickListener(h.onClick); // This would require addClickListener to expect `this: Handler`
                                       // Or use h.onClick.bind(h)
```

## 7. Overloads <a name="overloads"></a>
TypeScript allows you to declare function overloads. This means you can define multiple function signatures for the same function name, and TypeScript will pick the correct one based on the arguments provided.

The implementation signature (the one with the actual function body) must be general enough to be compatible with all overload signatures. Typically, the implementation uses `any` or union types for parameters and checks types internally.

```typescript
// Overload signatures
function makeDate(timestamp: number): Date;
function makeDate(m: number, d: number, y: number): Date;

// Implementation signature (must be compatible with overloads)
function makeDate(mOrTimestamp: number, d?: number, y?: number): Date {
  if (d !== undefined && y !== undefined) {
    return new Date(y, mOrTimestamp, d);
  }
  return new Date(mOrTimestamp);
}

const d1 = makeDate(1234567890); // Uses first overload
const d2 = makeDate(5, 5, 2025);   // Uses second overload
// const d3 = makeDate(5, 5); // Error: No overload matches this call.

console.log(d1);
console.log(d2);
```
When writing an overloaded function, the implementation signature is not visible from the outside. When calling an overloaded function, TypeScript will only consider the overload signatures.

## 8. Key Takeaways <a name="key-takeaways-functions"></a>
*   TypeScript allows explicit typing of function parameters and return values.
*   Function types can be defined to describe function signatures.
*   Optional (`?`), default-initialized, and rest (`...`) parameters provide flexibility in function definitions.
*   Arrow functions capture `this` from their surrounding scope, while `this` parameters can explicitly type `this` in regular functions.
*   Function overloads allow defining multiple signatures for a single function, with a common implementation.

These features make functions in TypeScript more robust, predictable, and easier to work with, especially in larger codebases.

---

⬅️ [Back to Classes](../04-ts-classes/README.md) | ➡️ [Next: Generics](../06-ts-generics/README.md)
