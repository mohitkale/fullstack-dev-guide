# 24. TypeScript Symbols and Unique Symbols

Introduced in ECMAScript 2015 (ES6), Symbols are a primitive data type in JavaScript, just like numbers, strings, and booleans. They are unique and immutable. TypeScript fully supports Symbols and also introduces a special subtype, `unique symbol`, which provides stronger guarantees of uniqueness at the type level.

This tutorial explores Symbols, their characteristics, how to use them in TypeScript, the concept of `unique symbol`, and their practical applications.

## Table of Contents
1.  [What are Symbols?](#what-are-symbols)
    *   Creating Symbols: `Symbol()`
    *   Optional Description
    *   Uniqueness of Symbols
2.  [Using Symbols as Property Keys](#symbols-as-property-keys)
    *   Avoiding Name Collisions
    *   Symbols are not Enumerable in `for...in` loops
    *   Accessing Symbol Properties: `Object.getOwnPropertySymbols()`
3.  [Well-Known Symbols](#well-known-symbols)
    *   Built-in JavaScript behavior customization
    *   Examples:
        *   `Symbol.iterator`: Making objects iterable.
        *   `Symbol.hasInstance`: Customizing `instanceof` behavior.
        *   `Symbol.toStringTag`: Customizing `Object.prototype.toString.call()` output.
        *   `Symbol.asyncIterator`, `Symbol.match`, `Symbol.replace`, etc.
4.  [Global Symbol Registry](#global-symbol-registry)
    *   `Symbol.for(key)`: Creating or retrieving symbols from the global registry.
    *   `Symbol.keyFor(sym)`: Retrieving a key for a global symbol.
    *   Use Cases: Sharing symbols across different parts of an application or realms.
5.  [TypeScript's `unique symbol` Type](#unique-symbol-type)
    *   What is a `unique symbol`?
    *   How to obtain a `unique symbol` type (const declarations, readonly static properties)
    *   Guarantees and Use Cases
    *   Cannot be created with `Symbol.for()`
6.  [Practical Use Cases for Symbols in TypeScript](#practical-use-cases)
    *   Creating "Private-like" Properties (Pseudo-privacy)
    *   Defining Unique Constants
    *   Implementing Protocols/Interfaces with Well-Known Symbols
    *   Metaprogramming and Library Authoring
7.  [Symbols in `for...of` and Iteration Protocols](#symbols-for-of)
8.  [Type Checking with Symbols](#type-checking-symbols)
9.  [Limitations and Considerations](#limitations-symbols)
10. [Key Takeaways](#key-takeaways-symbols)

## 1. What are Symbols? <a name="what-are-symbols"></a>
A Symbol is a primitive data type whose instances are unique and immutable. They are often used as keys for object properties to avoid name collisions between properties added by different libraries or parts of a codebase.

### Creating Symbols: `Symbol()`
You create a symbol by calling the `Symbol()` function (not with `new`):
```typescript
const sym1 = Symbol();
const sym2 = Symbol();

console.log(sym1 === sym2); // false, symbols are unique
```

### Optional Description
You can provide an optional string description for a symbol, mainly for debugging purposes. The description does not affect its uniqueness.
```typescript
const symWithDesc = Symbol("myDescription");
console.log(symWithDesc.toString()); // "Symbol(myDescription)"

const symA = Symbol("key");
const symB = Symbol("key");
console.log(symA === symB); // false, descriptions don't make them the same
```

### Uniqueness of Symbols
Every time `Symbol()` is called, a new, unique symbol is created. This uniqueness is guaranteed.

## 2. Using Symbols as Property Keys <a name="symbols-as-property-keys"></a>
Symbols can be used as keys for object properties, similar to strings.

```typescript
const idSymbol = Symbol("id");

let user = {
  name: "Alice",
  [idSymbol]: 12345 // Using a symbol as a computed property key
};

console.log(user[idSymbol]); // 12345
console.log(user.name);      // "Alice"
```

### Avoiding Name Collisions
This is a primary use case. If multiple scripts or libraries add properties to the same object, using symbols as keys prevents accidental overwriting of properties.

```typescript
// Library A
const libASymbol = Symbol("data");
function setDataLibA(obj: any, value: any) {
  obj[libASymbol] = value;
}

// Library B
const libBSymbol = Symbol("data"); // Different symbol, even with same description
function setDataLibB(obj: any, value: any) {
  obj[libBSymbol] = value;
}

let myObject = {};
setDataLibA(myObject, "Data from Lib A");
setDataLibB(myObject, "Data from Lib B");

console.log(myObject[libASymbol]); // "Data from Lib A"
console.log(myObject[libBSymbol]); // "Data from Lib B"
console.log(Object.keys(myObject)); // [] - symbol keys are not in Object.keys
```

### Symbols are not Enumerable in `for...in` loops
Symbol-keyed properties are not included in `for...in` loops or `Object.keys()` results.

```typescript
const symKey = Symbol("hidden");
let obj = {
  visible: "I am visible",
  [symKey]: "I am hidden from for...in"
};

for (const key in obj) {
  console.log(key); // Only "visible"
}

console.log(Object.keys(obj)); // ["visible"]
```

### Accessing Symbol Properties: `Object.getOwnPropertySymbols()`
To get an array of an object's own symbol properties, use `Object.getOwnPropertySymbols()`.
```typescript
console.log(Object.getOwnPropertySymbols(obj)); // [Symbol(hidden)]
console.log(obj[Object.getOwnPropertySymbols(obj)[0]]); // "I am hidden from for...in"

// To get all own keys (string and symbol):
// Reflect.ownKeys(obj) includes both string and symbol keys.
console.log(Reflect.ownKeys(obj)); // ["visible", Symbol(hidden)]
```

## 3. Well-Known Symbols <a name="well-known-symbols"></a>
JavaScript provides a set of built-in, pre-defined symbols known as "well-known symbols." These symbols are static properties of the `Symbol` object (e.g., `Symbol.iterator`). They allow developers to customize certain built-in JavaScript behaviors by implementing methods with these symbol keys on their objects.

### Examples:
*   **`Symbol.iterator`**: A method that returns an object's default iterator. Used by `for...of` loops.
    ```typescript
    class MyIterable {
      private items = [1, 2, 3];
      *[Symbol.iterator]() { // Using a generator function for convenience
        for (const item of this.items) {
          yield item;
        }
      }
    }
    const iterable = new MyIterable();
    for (const val of iterable) {
      console.log(val); // 1, 2, 3
    }
    ```
*   **`Symbol.hasInstance`**: A method that determines if a constructor object recognizes an object as its instance. Used by the `instanceof` operator.
    ```typescript
    class MyArray {
      static [Symbol.hasInstance](instance: any) {
        return Array.isArray(instance);
      }
    }
    console.log([] instanceof (MyArray as any)); // true
    ```
*   **`Symbol.toStringTag`**: A string value used for the default description of an object. Queried by `Object.prototype.toString.call()`.
    ```typescript
    class MyCustomType {
      get [Symbol.toStringTag]() {
        return "MyCustomType";
      }
    }
    const custom = new MyCustomType();
    console.log(Object.prototype.toString.call(custom)); // "[object MyCustomType]"
    ```
Other well-known symbols include `Symbol.asyncIterator`, `Symbol.match`, `Symbol.replace`, `Symbol.search`, `Symbol.split`, `Symbol.toPrimitive`, `Symbol.unscopables`, etc.

## 4. Global Symbol Registry <a name="global-symbol-registry"></a>
Besides creating unique symbols with `Symbol()`, JavaScript provides a global symbol registry.

*   **`Symbol.for(key: string)`**: Searches for existing symbols in the global registry with the given string `key` and returns it if found. Otherwise, a new symbol is created with this `key`, added to the global registry, and then returned.
    ```typescript
    const globalSym1 = Symbol.for("sharedKey");
    const globalSym2 = Symbol.for("sharedKey");
    console.log(globalSym1 === globalSym2); // true
    ```
*   **`Symbol.keyFor(sym: symbol)`**: Retrieves the string key for a given symbol from the global registry. If the symbol is not in the global registry (i.e., it was created with `Symbol()` or is not a global symbol), it returns `undefined`.
    ```typescript
    console.log(Symbol.keyFor(globalSym1)); // "sharedKey"

    const localSym = Symbol("localKey");
    console.log(Symbol.keyFor(localSym)); // undefined
    ```
**Use Cases**: Sharing symbols across different parts of an application, different iframes, or service workers, where you need to ensure you're using the exact same symbol instance based on a known string key.

## 5. TypeScript's `unique symbol` Type <a name="unique-symbol-type"></a>
TypeScript introduces a special subtype of `symbol` called `unique symbol`. A `unique symbol` is a symbol that is known to be unique at compile time.

### What is a `unique symbol`?
It's a type that only refers to a specific, constant symbol value. This provides stronger type safety because TypeScript knows exactly which symbol it is.

### How to obtain a `unique symbol` type:
1.  Declare a `const` variable initialized with `Symbol()`.
2.  Declare a `readonly static` property initialized with `Symbol()`.

```typescript
// 1. Const declaration
const uniqueSymA: unique symbol = Symbol("a");
// Type of uniqueSymA is 'typeof uniqueSymA'

// If not explicitly typed as 'unique symbol', it's inferred:
const uniqueSymB = Symbol("b");
// Type of uniqueSymB is also 'typeof uniqueSymB' (a unique symbol type)

// 2. Readonly static property
class MyConstants {
  static readonly StaticUniqueSymbol: unique symbol = Symbol("static");
}
// Type of MyConstants.StaticUniqueSymbol is 'typeof MyConstants.StaticUniqueSymbol'

// Contrast with a 'let' or non-readonly static property:
let regularSym: symbol = Symbol("c"); // Type is 'symbol', not 'unique symbol'
```

### Guarantees and Use Cases
`unique symbol` types are useful when you want to ensure that a property key is *exactly* a specific symbol and not just any symbol. This is often used in discriminated unions or when defining unique brands for nominal typing.

```typescript
const brandSymbol: unique symbol = Symbol("brand");

interface BrandedType {
  [brandSymbol]: void; // Using the unique symbol as a key
  data: string;
}

function createBranded(data: string): BrandedType {
  return { [brandSymbol]: undefined, data };
}

const item = createBranded("test");

// This function expects an object with the specific 'brandSymbol'
function processBranded(bt: BrandedType) {
  console.log(bt.data, bt[brandSymbol]);
}
processBranded(item);

// const otherSymbol = Symbol("brand");
// const wrongItem = { [otherSymbol]: undefined, data: "wrong" };
// processBranded(wrongItem); // Error: Type '{ [otherSymbol]: undefined; data: string; }'
                           // is not assignable to type 'BrandedType'.
                           // Property '[brandSymbol]' is missing.
```

### Cannot be created with `Symbol.for()`
Symbols created with `Symbol.for()` are not `unique symbol`s because their uniqueness depends on the runtime state of the global registry, not just their declaration.
```typescript
const globalNotUnique = Symbol.for("global"); // Type is 'symbol', not 'unique symbol'
```

## 6. Practical Use Cases for Symbols in TypeScript <a name="practical-use-cases"></a>
*   **Creating "Private-like" Properties (Pseudo-privacy)**: Since symbol-keyed properties are not easily discoverable (not in `for...in` or `Object.keys`), they can be used to store internal state in a class that is less likely to be accidentally accessed or modified from outside. This is not true privacy, as `Object.getOwnPropertySymbols()` can still reveal them.
    ```typescript
    const _internalData = Symbol("internalData");
    class MyClass {
      private [_internalData]: string;
      constructor(data: string) {
        this[_internalData] = data;
      }
      getData() {
        return this[_internalData];
      }
    }
    const instance = new MyClass("secret");
    console.log(instance.getData()); // "secret"
    // console.log(instance[_internalData]); // Error: _internalData is not defined (if symbol not exported)
    // If _internalData is exported, it can be accessed: instance[_internalData]
    ```
*   **Defining Unique Constants**: `unique symbol`s are excellent for defining a set of unique constant values that won't clash.
    ```typescript
    const LogLevelInfo: unique symbol = Symbol("Info");
    const LogLevelWarning: unique symbol = Symbol("Warning");
    const LogLevelError: unique symbol = Symbol("Error");
    _type LogLevel = typeof LogLevelInfo | typeof LogLevelWarning | typeof LogLevelError;

    function log(level: LogLevel, message: string) {
      // ...
    }
    log(LogLevelInfo, "Application started.");
    ```
*   **Implementing Protocols/Interfaces with Well-Known Symbols**: Customizing built-in JavaScript behavior (e.g., iteration, type coercion).
*   **Metaprogramming and Library Authoring**: Libraries can use symbols to add metadata or behavior to objects without interfering with user-defined properties.

## 7. Symbols in `for...of` and Iteration Protocols <a name="symbols-for-of"></a>
As seen with `Symbol.iterator`, symbols are fundamental to JavaScript's iteration protocols. The `for...of` loop relies on an object having a method keyed by `Symbol.iterator` that returns an iterator object.

## 8. Type Checking with Symbols <a name="type-checking-symbols"></a>
*   The type `symbol` refers to any symbol.
*   A `unique symbol` type refers to a specific symbol instance.

```typescript
let s1: symbol = Symbol("s1");
let s2: symbol = Symbol("s2");

const us1: unique symbol = Symbol("us1");
const us2: unique symbol = Symbol("us2");

s1 = s2; // OK
s1 = us1; // OK, unique symbol is assignable to symbol

// us1 = us2; // Error: Type 'typeof us2' is not assignable to type 'typeof us1'.
// us1 = s1;  // Error: Type 'symbol' is not assignable to type 'typeof us1'.
```

## 9. Limitations and Considerations <a name="limitations-symbols"></a>
*   **JSON Serialization**: `JSON.stringify()` ignores symbol-keyed properties.
    ```typescript
    const objWithSymbol = { name: "Test", [Symbol("id")]: 1 };
    console.log(JSON.stringify(objWithSymbol)); // "{\"name\":\"Test\"}"
    ```
*   **Not True Privacy**: While symbols can hide properties from common enumeration methods, they are discoverable via `Object.getOwnPropertySymbols()` or `Reflect.ownKeys()`.
*   **Complexity**: Introducing symbols can add complexity if not used judiciously. Their primary benefit is avoiding name collisions and implementing well-known behaviors.

## 10. Key Takeaways <a name="key-takeaways-symbols"></a>
*   Symbols are a unique and immutable primitive data type in JavaScript and TypeScript.
*   They are primarily used as non-colliding property keys and for customizing built-in JavaScript behavior via **Well-Known Symbols**.
*   Symbol-keyed properties are not enumerated by `for...in` or `Object.keys()`, but can be accessed with `Object.getOwnPropertySymbols()` and `Reflect.ownKeys()`.
*   The **Global Symbol Registry** (`Symbol.for()`, `Symbol.keyFor()`) allows sharing symbols across realms.
*   TypeScript introduces the **`unique symbol`** type for symbols that are constant and guaranteed unique at compile time, offering stronger type safety.
*   Symbols are crucial for iteration protocols (`Symbol.iterator`) and other metaprogramming tasks.

Understanding symbols and `unique symbol`s allows for more robust and advanced patterns in TypeScript, especially when dealing with object extension, library interoperability, and customizing core language behaviors.

---

⬅️ [Back to TypeScript Advanced Control Flow Analysis](../23-ts-advanced-control-flow/README.md) | ➡️ [Next: TypeScript and ES Modules vs. Namespaces (Final Verdict)](../25-ts-modules-vs-namespaces-final/README.md) *(Placeholder)*
