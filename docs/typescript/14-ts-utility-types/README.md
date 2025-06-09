# 14. TypeScript Utility Types

TypeScript comes with a set of built-in utility types that provide common type transformations. These utilities help you create new types based on existing ones in a concise and powerful way, reducing boilerplate and improving type safety.

## Table of Contents
1.  [What are Utility Types?](#what-are-utility-types)
2.  [Property Modifiers](#property-modifiers)
    *   [`Partial<Type>`](#partial)
    *   [`Required<Type>`](#required)
    *   [`Readonly<Type>`](#readonly)
3.  [Property Selection/Exclusion](#property-selection-exclusion)
    *   [`Pick<Type, Keys>`](#pick)
    *   [`Omit<Type, Keys>`](#omit)
4.  [Set-like Operations on Union Types](#set-like-operations)
    *   [`Exclude<UnionType, ExcludedMembers>`](#exclude)
    *   [`Extract<Type, Union>`](#extract)
5.  [Nullable Types](#nullable-types-utility)
    *   [`NonNullable<Type>`](#nonnullable)
6.  [Record and Dictionary Types](#record-dictionary-types)
    *   [`Record<Keys, Type>`](#record)
7.  [Function and Constructor Related Types](#function-constructor-types)
    *   [`Parameters<Type>`](#parameters)
    *   [`ConstructorParameters<Type>`](#constructorparameters)
    *   [`ReturnType<Type>`](#returntype)
    *   [`InstanceType<Type>`](#instancetype)
8.  [String Manipulation Types](#string-manipulation-types)
    *   [`Uppercase<StringType>`](#uppercase)
    *   [`Lowercase<StringType>`](#lowercase)
    *   [`Capitalize<StringType>`](#capitalize)
    *   [`Uncapitalize<StringType>`](#uncapitalize)
9.  [Combining Utility Types](#combining-utility-types)
10. [Key Takeaways](#key-takeaways-utility-types)

## 1. What are Utility Types? <a name="what-are-utility-types"></a>
Utility types are generic types provided by TypeScript that take one or more types as input and produce a new type as output. They are implemented using advanced type features like mapped types and conditional types under the hood, but you can use them without needing to understand their internal implementation immediately.

## 2. Property Modifiers <a name="property-modifiers"></a>
These utilities change the properties of an existing object type.

### `Partial<Type>` <a name="partial"></a>
Constructs a type with all properties of `Type` set to optional. This is useful for representing objects where some properties might be missing, like in update operations.

```typescript
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

function updateTodo(todo: Todo, fieldsToUpdate: Partial<Todo>) {
  return { ...todo, ...fieldsToUpdate };
}

const todo1: Todo = {
  title: "Learn TypeScript",
  description: "Study utility types",
  completed: false,
};

const updatedTodo = updateTodo(todo1, { description: "Master utility types" });
console.log(updatedTodo);
// { title: 'Learn TypeScript', description: 'Master utility types', completed: false }
```
`Partial<Todo>` becomes `{ title?: string; description?: string; completed?: boolean; }`.

### `Required<Type>` <a name="required"></a>
Constructs a type with all properties of `Type` set to required. This is the opposite of `Partial`.

```typescript
interface Props {
  a?: number;
  b?: string;
}

const obj: Props = { a: 5 };
// const obj2: Required<Props> = { a: 5 }; // Error: Property 'b' is missing
const obj3: Required<Props> = { a: 5, b: "hello" }; // OK
```
`Required<Props>` becomes `{ a: number; b: string; }`.

### `Readonly<Type>` <a name="readonly"></a>
Constructs a type with all properties of `Type` set to `readonly`, meaning they cannot be reassigned after creation.

```typescript
interface Point {
  x: number;
  y: number;
}

const p1: Readonly<Point> = { x: 10, y: 20 };
// p1.x = 5; // Error: Cannot assign to 'x' because it is a read-only property.
```
`Readonly<Point>` becomes `{ readonly x: number; readonly y: number; }`.

## 3. Property Selection/Exclusion <a name="property-selection-exclusion"></a>
These utilities create new types by picking or omitting properties from an existing type.

### `Pick<Type, Keys>` <a name="pick"></a>
Constructs a type by picking the set of properties `Keys` (a string literal or union of string literals) from `Type`.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

type UserPreview = Pick<User, "name" | "email">;
// UserPreview is { name: string; email: string; }

const userDisplay: UserPreview = {
  name: "Alice",
  email: "alice@example.com",
  // age: 30 // Error: 'age' does not exist on type 'UserPreview'
};
```

### `Omit<Type, Keys>` <a name="omit"></a>
Constructs a type by picking all properties from `Type` and then removing `Keys`.

```typescript
interface Product {
  id: string;
  name: string;
  description: string;
  price: number;
  inStock: boolean;
}

type ProductCreationData = Omit<Product, "id" | "inStock">;
// ProductCreationData is { name: string; description: string; price: number; }

const newProduct: ProductCreationData = {
  name: "Laptop",
  description: "High-performance laptop",
  price: 1200,
  // id: "123" // Error
};
```

## 4. Set-like Operations on Union Types <a name="set-like-operations"></a>
These utilities perform set-like operations (exclusion, extraction) on union types.

### `Exclude<UnionType, ExcludedMembers>` <a name="exclude"></a>
Constructs a type by excluding from `UnionType` all union members that are assignable to `ExcludedMembers`.

```typescript
type Status = "pending" | "processing" | "completed" | "failed";
type ActiveStatus = Exclude<Status, "completed" | "failed">;
// ActiveStatus is "pending" | "processing"

let currentStatus: ActiveStatus = "pending";
// currentStatus = "completed"; // Error
```

### `Extract<Type, Union>` <a name="extract"></a>
Constructs a type by extracting from `Type` all union members that are assignable to `Union`.

```typescript
type EventType = "click" | "hover" | "submit" | { type: "custom"; payload: any };
type MouseEvents = Extract<EventType, "click" | "hover">;
// MouseEvents is "click" | "hover"

type CustomEventObject = Extract<EventType, { type: "custom" }>;
// CustomEventObject is { type: "custom"; payload: any }
```

## 5. Nullable Types <a name="nullable-types-utility"></a>

### `NonNullable<Type>` <a name="nonnullable"></a>
Constructs a type by excluding `null` and `undefined` from `Type`.

```typescript
type MaybeString = string | null | undefined;
type DefinitelyString = NonNullable<MaybeString>;
// DefinitelyString is string

let myString: DefinitelyString = "Hello";
// myString = null; // Error
```

## 6. Record and Dictionary Types <a name="record-dictionary-types"></a>

### `Record<Keys, Type>` <a name="record"></a>
Constructs an object type whose property keys are `Keys` and whose property values are `Type`. Useful for creating dictionaries or maps.

```typescript
interface CatInfo {
  age: number;
  breed: string;
}

type CatName = "miffy" | "boris" | "mordred";

const cats: Record<CatName, CatInfo> = {
  miffy: { age: 10, breed: "Persian" },
  boris: { age: 5, breed: "Maine Coon" },
  mordred: { age: 16, breed: "British Shorthair" }
};

console.log(cats.miffy.age); // 10

// Another example: mapping string keys to numbers
type StringToNumberMap = Record<string, number>;
const scores: StringToNumberMap = {
  alice: 100,
  bob: 90,
  // charlie: "80" // Error: Type 'string' is not assignable to type 'number'.
};
```

## 7. Function and Constructor Related Types <a name="function-constructor-types"></a>
These utilities extract type information from functions and constructors.

### `Parameters<Type>` <a name="parameters"></a>
Constructs a tuple type from the types used in the parameters of a function type `Type`.

```typescript
function greet(name: string, age: number): void {
  console.log(`Hello ${name}, you are ${age} years old.`);
}

type GreetParams = Parameters<typeof greet>;
// GreetParams is [name: string, age: number]

const params: GreetParams = ["Alice", 30];
greet(...params);
```

### `ConstructorParameters<Type>` <a name="constructorparameters"></a>
Constructs a tuple or array type from the types of a constructor function type. It produces a tuple type with all the parameter types (or `never` if `Type` is not a function).

```typescript
class Person {
  constructor(public name: string, public age: number) {}
}

type PersonConstructorParams = ConstructorParameters<typeof Person>;
// PersonConstructorParams is [name: string, age: number]

const personArgs: PersonConstructorParams = ["Bob", 42];
const bob = new Person(...personArgs);
```

### `ReturnType<Type>` <a name="returntype"></a>
Constructs a type consisting of the return type of function `Type`.

```typescript
function createId(): string {
  return Math.random().toString(36).substr(2, 9);
}

type IdType = ReturnType<typeof createId>;
// IdType is string

const newId: IdType = createId();
```

### `InstanceType<Type>` <a name="instancetype"></a>
Constructs a type consisting of the instance type of a constructor function type `Type`.

```typescript
class Car {
  constructor(public make: string, public model: string) {}
  display() {
    console.log(`${this.make} ${this.model}`);
  }
}

type CarInstance = InstanceType<typeof Car>;
// CarInstance is Car (the class itself, representing an instance)

const myCar: CarInstance = new Car("Toyota", "Camry");
myCar.display();
```

## 8. String Manipulation Types <a name="string-manipulation-types"></a>
TypeScript 4.1 introduced several utility types for manipulating string literal types.

### `Uppercase<StringType>` <a name="uppercase"></a>
Converts each character in the string literal type to its uppercase equivalent.
```typescript
type Greeting = "hello world";
type LoudGreeting = Uppercase<Greeting>; // "HELLO WORLD"
```

### `Lowercase<StringType>` <a name="lowercase"></a>
Converts each character in the string literal type to its lowercase equivalent.
```typescript
type Shout = "HEY THERE!";
type Whisper = Lowercase<Shout>; // "hey there!"
```

### `Capitalize<StringType>` <a name="capitalize"></a>
Converts the first character in the string literal type to uppercase.
```typescript
type Name = "alice";
type CapitalizedName = Capitalize<Name>; // "Alice"
```

### `Uncapitalize<StringType>` <a name="uncapitalize"></a>
Converts the first character in the string literal type to lowercase.
```typescript
type Title = "Manager";
type LowercaseTitle = Uncapitalize<Title>; // "manager"
```

## 9. Combining Utility Types <a name="combining-utility-types"></a>
Utility types can be nested and combined to create more complex type transformations.

```typescript
interface FullUser {
  id: number;
  username: string;
  email?: string;
  profile: {
    bio: string;
    avatarUrl?: string;
  };
}

// Make all properties of FullUser optional, then pick only 'username' and 'profile',
// and then make the 'profile' property's properties also optional.

// Step 1: Make all properties of FullUser optional
type PartialUser = Partial<FullUser>;
// { id?: number; username?: string; email?: string; profile?: { bio: string; avatarUrl?: string; }; }

// Step 2: Pick 'username' and 'profile' from PartialUser
type PickedUser = Pick<PartialUser, "username" | "profile">;
// { username?: string; profile?: { bio: string; avatarUrl?: string; }; }

// Step 3: Make the 'profile' property (if it exists) also have optional properties
type UserForm = Omit<PickedUser, "profile"> & {
  profile?: Partial<NonNullable<PickedUser["profile"]>>;
};
/*
UserForm is:
{
  username?: string;
  profile?: {
    bio?: string;
    avatarUrl?: string;
  }
}
*/

const userFormData: UserForm = {
  username: "testuser",
  profile: {
    bio: "Loves TypeScript"
  }
};
```

## 10. Key Takeaways <a name="key-takeaways-utility-types"></a>
*   Utility types are built-in generics that transform existing types.
*   They help in creating precise and flexible types without manual repetition.
*   Common categories include:
    *   Property modifiers (`Partial`, `Required`, `Readonly`).
    *   Property selection/exclusion (`Pick`, `Omit`).
    *   Union type operations (`Exclude`, `Extract`, `NonNullable`).
    *   Dictionary creation (`Record`).
    *   Function/constructor introspection (`Parameters`, `ConstructorParameters`, `ReturnType`, `InstanceType`).
    *   String literal manipulation (`Uppercase`, `Lowercase`, `Capitalize`, `Uncapitalize`).
*   Mastering utility types significantly enhances your ability to work effectively with TypeScript's type system.

---

⬅️ [Back to Type Checking JavaScript Files](../13-ts-type-checking-javascript/README.md) | ➡️ [Next: TypeScript with React (TSX)](../15-ts-react-tsx/README.md)
