# 06. TypeScript Generics

Generics are a powerful feature in TypeScript (and other statically-typed languages) that allow you to write reusable code components that can work over a variety of types rather than a single one. This enables you to create flexible and type-safe functions, classes, and interfaces.

This tutorial explores the concept of generics and how to use them effectively in TypeScript.

## Table of Contents
1.  [The Problem: Why Generics?](#why-generics)
2.  [Hello World of Generics: The Identity Function](#hello-generics-identity)
3.  [Working with Generic Type Variables](#working-with-generic-type-variables)
4.  [Generic Types](#generic-types)
    *   [Generic Function Types](#generic-function-types)
    *   [Generic Interfaces](#generic-interfaces)
    *   [Generic Classes](#generic-classes)
5.  [Generic Constraints](#generic-constraints)
    *   [Using `extends` to Constrain Types](#constraints-extends)
6.  [Using Type Parameters in Generic Constraints](#type-params-in-constraints)
7.  [Using Class Types in Generics](#class-types-in-generics)
8.  [Key Takeaways](#key-takeaways-generics)

## 1. The Problem: Why Generics? <a name="why-generics"></a>
Imagine you want to write a function that returns whatever is passed to it (an identity function). Without generics, you might write it using `any`:

```typescript
function identityAny(arg: any): any {
  return arg;
}

let outputAny = identityAny("myString"); // outputAny is of type 'any'
// We lose type information. outputAny could be a string, number, etc.
// console.log(outputAny.length); // OK at compile time, but if arg was a number, this would fail at runtime.
```
Using `any` is not ideal because it causes the function to lose information about the type of the argument and the return value. We don't know if `outputAny` should have a `length` property, for example.

Generics solve this by allowing us to capture the type of the argument in a way that we can also use to denote what is being returned.

## 2. Hello World of Generics: The Identity Function <a name="hello-generics-identity"></a>
Here's how you'd write the identity function using generics:

```typescript
function identity<T>(arg: T): T {
  return arg;
}
```
*   `<T>`: This declares a **type variable** `T`. `T` is a placeholder for the actual type that will be provided when the function is called.
*   `arg: T`: The argument `arg` will be of type `T`.
*   `: T`: The function will return a value of type `T`.

Now, when we use this function:

```typescript
// Explicitly setting T to string
let outputString = identity<string>("myString");
console.log(outputString.length); // OK, outputString is 'string'

// Type argument inference (TypeScript infers T as number)
let outputNumber = identity(100);
// console.log(outputNumber.length); // Error: Property 'length' does not exist on type 'number'.
console.log(outputNumber.toFixed(2)); // OK, outputNumber is 'number'
```
TypeScript can infer the type `T` based on the argument passed, or you can explicitly specify it using angle brackets (`<type>`). The key is that we preserve the type information.

## 3. Working with Generic Type Variables <a name="working-with-generic-type-variables"></a>
When you start using generics, you might be tempted to assume that `T` has certain properties. For example, if `T` is an array, you might want to access its `length` property.

```typescript
function loggingIdentity<T>(arg: T): T {
  // console.log(arg.length); // Error: Property 'length' does not exist on type 'T'.
  return arg;
}
```
The error occurs because `T` could be any type, and not all types have a `length` property. To fix this, we can specify that `T` is an array of some element type:

```typescript
function loggingIdentityArray<T>(arg: T[]): T[] {
  console.log(arg.length); // OK, T[] (or Array<T>) has a .length property
  return arg;
}

loggingIdentityArray([1, 2, 3]); // T is inferred as number

// Alternative syntax
function loggingIdentityArrayAlt<T>(arg: Array<T>): Array<T> {
  console.log(arg.length);
  return arg;
}
loggingIdentityArrayAlt<string>(["a", "b"]); // T is explicitly string
```

## 4. Generic Types <a name="generic-types"></a>
We can also create generic interfaces, classes, and function types.

### Generic Function Types <a name="generic-function-types"></a>
The type of a generic function is similar to that of a non-generic function, with the type parameters listed first, similarly to function declarations:

```typescript
// Generic function type for our identity function
let myIdentity: <T>(arg: T) => T = identity;

// We can also use a different name for the generic type parameter in the type
let anotherIdentity: <U>(input: U) => U = identity;

// We can also write the generic type as a call signature of an object literal type
let yetAnotherIdentity: { <T>(arg: T): T } = identity;

console.log(myIdentity("test")); // "test"
```

### Generic Interfaces <a name="generic-interfaces"></a>
Interfaces can also be generic.

```typescript
interface GenericIdentityFn<T> {
  (arg: T): T;
}

let identityFromInterface: GenericIdentityFn<number> = identity;
console.log(identityFromInterface(123)); // 123
// console.log(identityFromInterface("hello")); // Error: Argument of type 'string' is not assignable to parameter of type 'number'.

interface Pair<K, V> {
  key: K;
  value: V;
}

let kvp1: Pair<number, string> = { key: 1, value: "Apple" };
let kvp2: Pair<string, boolean> = { key: "isEnabled", value: true };

console.log(kvp1);
console.log(kvp2);
```

### Generic Classes <a name="generic-classes"></a>
A generic class has a similar shape to a generic interface. Generic type parameters are listed in angle brackets (`<>`) following the name of the class.

```typescript
class GenericNumber<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;

  constructor(zero: T, addFn: (x: T, y: T) => T) {
    this.zeroValue = zero;
    this.add = addFn;
  }
}

let myGenericNumber = new GenericNumber<number>(0, (x, y) => x + y);
console.log(myGenericNumber.add(5, 10)); // 15

let myGenericString = new GenericNumber<string>("", (x, y) => x + y);
console.log(myGenericString.add("hello ", "world")); // "hello world"

// Note: Static members of a generic class cannot use the class's type parameter.
// class Box<T> {
//   static defaultValue: T; // Error: Static members cannot reference class type parameters.
//   contents: T;
//   constructor(value: T) {
//     this.contents = value;
//   }
// }
```

## 5. Generic Constraints <a name="generic-constraints"></a>
Sometimes, you want to limit the types that can be used with a generic type parameter. For example, you might want a generic function that works on types that have a `length` property.

### Using `extends` to Constrain Types <a name="constraints-extends"></a>
We can use the `extends` keyword to constrain the type parameter to types that have certain properties.

```typescript
interface Lengthwise {
  length: number;
}

function loggingIdentityWithConstraint<T extends Lengthwise>(arg: T): T {
  console.log(arg.length); // Now we know that T has a .length property, because it extends Lengthwise
  return arg;
}

loggingIdentityWithConstraint("hello"); // string has length
loggingIdentityWithConstraint([1, 2, 3]); // array has length
loggingIdentityWithConstraint({ length: 10, value: 3 }); // object literal with length property

// loggingIdentityWithConstraint(3); // Error: Argument of type 'number' is not assignable to parameter of type 'Lengthwise'.
// loggingIdentityWithConstraint({ value: 3 }); // Error: Property 'length' is missing
```
Now, `T` must be a type that is assignable to `Lengthwise`, meaning it must have a `length` property of type `number`.

## 6. Using Type Parameters in Generic Constraints <a name="type-params-in-constraints"></a>
You can declare a type parameter that is constrained by another type parameter.

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

let x = { a: 1, b: "hello", c: true };

let aValue = getProperty(x, "a"); // number
let bValue = getProperty(x, "b"); // string
// let dValue = getProperty(x, "d"); // Error: Argument of type '"d"' is not assignable to parameter of type '"a" | "b" | "c"'.

console.log(aValue);
console.log(bValue);
```
*   `T`: The type of the object.
*   `K extends keyof T`: `K` is a type parameter that must be a key of `T`. `keyof T` is the union of known, public property names of `T`.
*   `T[K]`: The return type is the type of the property `K` on `T` (indexed access type).

This pattern is very useful for type-safe property access.

## 7. Using Class Types in Generics <a name="class-types-in-generics"></a>
When creating factories in TypeScript using generics, it’s necessary to refer to class types by their constructor functions.

```typescript
function createInstance<T>(c: { new (): T }): T {
  return new c();
}

class BeeKeeper {
  hasMask: boolean = true;
}

class ZooKeeper {
  nametag: string = "Zoo Keeper";
}

class AnimalObj {
  numLegs: number = 4;
}

let keeper = createInstance(BeeKeeper); // T is BeeKeeper
let animal = createInstance(AnimalObj); // T is AnimalObj

console.log(keeper.hasMask); // true
console.log(animal.numLegs); // 4

// More advanced example with constructor parameters:
function createNamedInstance<T>(constructorFn: { new (name: string): T }, name: string): T {
    return new constructorFn(name);
}

class PersonNamed {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
}

let personInstance = createNamedInstance(PersonNamed, "Alice");
console.log(personInstance.name); // "Alice"
```
Here, `{ new (): T }` means "any constructor function that takes no arguments and produces an instance of type `T`".

## 8. Key Takeaways <a name="key-takeaways-generics"></a>
*   Generics provide a way to create reusable components that work over a variety of types.
*   Type variables (e.g., `<T>`) act as placeholders for actual types specified at call time.
*   TypeScript can often infer generic type arguments.
*   Generic interfaces, classes, and function types allow for flexible and type-safe structures.
*   Generic constraints (`extends`) limit the types that can be used with a type parameter, ensuring certain properties or methods exist.
*   `keyof` and indexed access types (`T[K]`) are powerful tools when working with generic constraints on object properties.
*   Generics can be used with class constructor types for factory patterns.

Generics are a fundamental concept for writing scalable and robust TypeScript code.

---

⬅️ [Back to Functions](../05-ts-functions/README.md) | ➡️ [Next: Enums and Literal Types](../07-ts-enums-literal-types/README.md)
