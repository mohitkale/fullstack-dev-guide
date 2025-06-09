# 03. TypeScript Interfaces

Interfaces are one of the core concepts in TypeScript and a powerful way to define contracts within your code, as well as contracts with code outside of your project. They are primarily used for "duck typing" or structural subtyping, meaning if an object has the shape an interface defines, it's considered to be of that interface's type.

This tutorial explores how to define and use interfaces in TypeScript.

## Table of Contents
1.  [What is an Interface?](#what-is-an-interface)
2.  [Our First Interface](#first-interface)
3.  [Optional Properties](#optional-properties)
4.  [Readonly Properties](#readonly-properties)
5.  [Excess Property Checks](#excess-property-checks)
6.  [Function Types](#function-types)
7.  [Indexable Types](#indexable-types)
    *   [String Index Signature](#string-index-signature)
    *   [Number Index Signature](#number-index-signature)
    *   [Readonly Index Signatures](#readonly-index-signatures)
8.  [Class Types](#class-types)
    *   [Implementing an Interface](#implementing-an-interface)
    *   [Difference between `static` and `instance` sides of a class](#static-vs-instance-class)
9.  [Extending Interfaces](#extending-interfaces)
10. [Hybrid Types](#hybrid-types)
11. [Interfaces Extending Classes](#interfaces-extending-classes)
12. [Key Takeaways](#key-takeaways-interfaces)

## 1. What is an Interface? <a name="what-is-an-interface"></a>
An interface in TypeScript is a way to define the shape of an object. It specifies what properties an object should have and what types those properties should be. Interfaces are purely a compile-time construct; they are not compiled into JavaScript and have no runtime representation.

Key uses:
*   Defining contracts for objects.
*   Ensuring objects meet a certain structure.
*   Describing the shapes of function types and class implementations.

## 2. Our First Interface <a name="first-interface"></a>
Let's define a simple interface that describes an object with a `label` property of type `string`.

```typescript
interface LabeledValue {
  label: string;
}

function printLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj); // OK, myObj has a 'label' property of type string

// let wrongObj = { size: 10 }; // Error: Property 'label' is missing
// printLabel(wrongObj);
```
The `LabeledValue` interface ensures that any object passed to `printLabel` must have a `label` property that is a string.

## 3. Optional Properties <a name="optional-properties"></a>
Not all properties of an interface may be required. You can mark properties as optional by adding a `?` at the end of their name.

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  let newSquare = { color: "white", area: 100 };
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare1 = createSquare({ color: "black" });
console.log(mySquare1); // { color: 'black', area: 100 }

let mySquare2 = createSquare({ width: 20 });
console.log(mySquare2); // { color: 'white', area: 400 }

let mySquare3 = createSquare({});
console.log(mySquare3); // { color: 'white', area: 100 }
```
Optional properties are useful when you have objects that might not have all properties set, or for implementing patterns like "option bags".

## 4. Readonly Properties <a name="readonly-properties"></a>
Some properties should only be modifiable when an object is first created. You can specify this by putting `readonly` before the name of the property.

```typescript
interface Point {
  readonly x: number;
  readonly y: number;
}

let p1: Point = { x: 10, y: 20 };
// p1.x = 5; // Error! Cannot assign to 'x' because it is a read-only property.

// TypeScript also has a `ReadonlyArray<T>` type that is the same as `Array<T>` with all mutating methods removed.
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;

// ro[0] = 12; // Error!
// ro.push(5); // Error!
// ro.length = 100; // Error!
// a = ro; // Error! Assigning ReadonlyArray<number> back to number[] is not allowed.
// However, you can override it with a type assertion:
// a = ro as number[];
```
`readonly` ensures that properties cannot be changed after an object is created.

## 5. Excess Property Checks <a name="excess-property-checks"></a>
When you pass object literals directly to a function or assign them to a variable whose type is an interface, TypeScript performs "excess property checking". If an object literal has any properties that the target type doesn't have, you’ll get an error.

```typescript
interface Config {
  color?: string;
  width?: number;
}

function configure(config: Config): void {
  // ...
}

// configure({ colour: "red", width: 100 }); // Error: Object literal may only specify known properties, and 'colour' does not exist in type 'Config'. Did you mean to write 'color'?

// To get around this, you can use a type assertion or assign the object to another variable first:
let options = { colour: "red", width: 100 }; // No error here
// configure(options); // Error: Argument of type '{ colour: string; width: number; }' is not assignable to parameter of type 'Config'.
                      // Object literal may only specify known properties, and 'colour' does not exist in type 'Config'.

// Correct way if 'colour' was a typo:
configure({ color: "red", width: 100 }); // OK

// If you need to pass extra properties and the interface allows it (e.g., with an index signature):
interface ConfigWithExtras {
  color?: string;
  width?: number;
  [propName: string]: any; // Allows any other properties of any type
}

function configureWithExtras(config: ConfigWithExtras): void {
  // ...
}
configureWithExtras({ colour: "red", width: 100, otherProp: true }); // OK due to index signature
```

## 6. Function Types <a name="function-types"></a>
Interfaces can describe function types. To do this, an interface is given a call signature. This is like a function declaration with only the parameter list and return type given.

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
  let result = src.search(sub);
  return result > -1;
};

// Parameter names do not need to match
let anotherSearch: SearchFunc;
anotherSearch = function(sourceText: string, searchTerm: string) { // Return type inferred as boolean
  return sourceText.search(searchTerm) !== -1;
};

console.log(mySearch("hello world", "world")); // true
console.log(anotherSearch("typescript", "java")); // false
```

## 7. Indexable Types <a name="indexable-types"></a>
Interfaces can describe types that we can "index into" like `a[10]`, or `ageMap["daniel"]`. Indexable types have an *index signature* that describes the types we can use to index into the object, along with the corresponding return types when indexing.

There are two types of supported index signatures: string and number.

### String Index Signature <a name="string-index-signature"></a>
This describes the type when an object is indexed with a string.

```typescript
interface StringArray {
  [index: string]: string; // All properties must be strings
}

let myArray: StringArray = {};
myArray["item1"] = "value1";
myArray["item2"] = "value2";
// myArray["item3"] = 10; // Error: Type 'number' is not assignable to type 'string'.

console.log(myArray["item1"]); // "value1"
```

### Number Index Signature <a name="number-index-signature"></a>
This describes the type when an object is indexed with a number. It's important to note that when indexing with a `number`, JavaScript will actually convert that to a `string` before indexing. This means that a `number` index signature must be assignable to a `string` index signature if both are present.

```typescript
interface NumberDictionary {
  [index: number]: string; // Values must be strings
  length: number; // OK, length is a number
  // name: string; // Error: Property 'name' of type 'string' is not assignable to string index type 'string | number'.
                  // If you have a numeric indexer, all properties must match the return type of that indexer.
}

let myDict: NumberDictionary = {
  0: "first",
  1: "second",
  length: 2
};

// If both string and number indexers are present:
interface MixedDictionary {
    [index: string]: any; // String indexer
    [index: number]: string; // Numeric indexer - its return type must be a subtype of string indexer's return type
}

let mixed: MixedDictionary = {};
mixed[0] = "test"; // OK, string is assignable to any
mixed["key"] = 123; // OK
```

### Readonly Index Signatures <a name="readonly-index-signatures"></a>
You can make index signatures `readonly` to prevent assignments to their indices after creation:

```typescript
interface ReadonlyStringArray {
  readonly [index: number]: string;
}

let roStrings: ReadonlyStringArray = ["Hello", "World"];
// roStrings[0] = "Hi"; // Error! Index signature in type 'ReadonlyStringArray' only permits reading.
```

## 8. Class Types <a name="class-types"></a>
Interfaces can be used to explicitly enforce that a class meets a particular contract.

### Implementing an Interface <a name="implementing-an-interface"></a>

```typescript
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) { /* ... */ }
}

let myClock = new Clock(10, 30);
myClock.setTime(new Date());
console.log(myClock.currentTime);
```
Interfaces describe the public side of the class, rather than both the public and private side. This prohibits you from using them to check that a class also has particular types for the private side of the class instance.

### Difference between `static` and `instance` sides of a class <a name="static-vs-instance-class"></a>
When working with classes and interfaces, it’s important to remember that an interface only describes the *instance* side of a class, not the *static* side. Therefore, you cannot use an interface to check for static members or constructors.

```typescript
interface ClockConstructor {
  new (hour: number, minute: number): ClockInstance;
}
interface ClockInstance {
  tick(): void;
}

// This function takes a class that implements ClockConstructor and creates an instance
function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInstance {
  return new ctor(hour, minute);
}

class DigitalClock implements ClockInstance {
  constructor(h: number, m: number) { /* ... */ }
  tick() {
    console.log("beep beep");
  }
}

class AnalogClock implements ClockInstance {
  constructor(h: number, m: number) { /* ... */ }
  tick() {
    console.log("tick tock");
  }
}

let digital = createClock(DigitalClock, 12, 17);
digital.tick();
let analog = createClock(AnalogClock, 7, 32);
analog.tick();

// This would cause an error because `DigitalClock` constructor doesn't match `ClockConstructor` directly.
// class BadClock {
//   constructor(h: string) {} // Constructor signature mismatch
//   tick() {}
// }
// let bad = createClock(BadClock, 1, 1); // Error
```

## 9. Extending Interfaces <a name="extending-interfaces"></a>
Like classes, interfaces can extend each other. This allows you to copy the members of one interface into another, which gives you more flexibility in how you separate your interfaces into reusable components.

```typescript
interface Shape {
  color: string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength: number;
}

let square = {} as Square; // Using type assertion for simplicity
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;

console.log(square); // { color: 'blue', sideLength: 10, penWidth: 5 }
```
An interface can extend multiple interfaces.

## 10. Hybrid Types <a name="hybrid-types"></a>
An interface can describe an object that acts as both a function and an object with additional properties.

```typescript
interface Counter {
  (start: number): string; // Call signature (function part)
  interval: number;         // Property (object part)
  reset(): void;            // Method (object part)
}

function getCounter(): Counter {
  let counter = ((start: number) => `Started at ${start}`) as Counter;
  counter.interval = 123;
  counter.reset = function () { /* ... */ };
  return counter;
}

let c = getCounter();
console.log(c(10));          // "Started at 10"
console.log(c.interval);     // 123
c.reset();
```

## 11. Interfaces Extending Classes <a name="interfaces-extending-classes"></a>
When an interface type extends a class type it inherits the members of the class but not their implementations. It is as if the interface had declared all of the members of the class. Interfaces inherit even the private and protected members of a base class. This means that when you create an interface that extends a class with private or protected members, only that class or a subclass of it can implement the interface.

```typescript
class Control {
  private state: any;
}

interface SelectableControl extends Control {
  select(): void;
}

class Button extends Control implements SelectableControl {
  select() { /* ... */ }
}

class TextBox extends Control {
  // select() { /* ... */ } // Not implementing SelectableControl
}

// Error: Property 'state' is missing in type 'ImageControl'.
// class ImageControl implements SelectableControl {
//   private state: any; // Must be compatible with Control's state
//   select() { /* ... */ }
// }
```
This is useful for scenarios where you need to ensure that an implementing class has the same private structure as a base class, often seen in mixin patterns or for deep inheritance checks.

## 12. Key Takeaways <a name="key-takeaways-interfaces"></a>
*   Interfaces define contracts for the shapes of objects, functions, and classes.
*   They are a compile-time construct and have no runtime JavaScript equivalent.
*   Properties can be optional (`?`) or readonly (`readonly`).
*   Interfaces can describe function types (call signatures) and indexable types (index signatures).
*   Classes can `implement` interfaces to ensure they adhere to a contract.
*   Interfaces can `extend` other interfaces to inherit their members.
*   Hybrid types allow an interface to describe objects that are both functions and have properties.
*   Interfaces can extend classes, inheriting their member declarations (including private/protected).

Interfaces are a cornerstone of TypeScript, enabling robust and flexible type checking.

---

⬅️ [Back to Basic Types](../02-ts-basic-types/README.md) | ➡️ [Next: Classes](../04-ts-classes/README.md)
