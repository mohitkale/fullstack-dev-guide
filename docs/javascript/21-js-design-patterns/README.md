# 21. JavaScript Design Patterns

Design patterns are reusable solutions to commonly occurring problems within a given context in software design. They are not specific algorithms or pieces of code, but rather general concepts or templates for how to structure code to solve a problem efficiently and effectively. Understanding design patterns can help you write more flexible, maintainable, and understandable JavaScript code.

## Table of Contents
1.  [What are Design Patterns?](#what-are-design-patterns)
    *   [Benefits of Using Design Patterns](#benefits-of-patterns)
2.  [Categories of Design Patterns](#categories-of-patterns)
    *   [Creational Patterns](#creational-patterns)
    *   [Structural Patterns](#structural-patterns)
    *   [Behavioral Patterns](#behavioral-patterns)
3.  [Common Creational Patterns in JavaScript](#common-creational)
    *   [Constructor Pattern](#constructor-pattern)
    *   [Factory Pattern](#factory-pattern)
    *   [Singleton Pattern](#singleton-pattern)
    *   [Prototype Pattern (Recap)](#prototype-pattern-recap)
    *   [Builder Pattern](#builder-pattern)
4.  [Common Structural Patterns in JavaScript](#common-structural)
    *   [Module Pattern (and ES6 Modules)](#module-pattern)
    *   [Adapter Pattern](#adapter-pattern)
    *   [Decorator Pattern](#decorator-pattern)
    *   [Facade Pattern](#facade-pattern)
    *   [Proxy Pattern](#proxy-pattern)
5.  [Common Behavioral Patterns in JavaScript](#common-behavioral)
    *   [Observer Pattern (Publish/Subscribe)](#observer-pattern)
    *   [Iterator Pattern (Recap)](#iterator-pattern-recap)
    *   [Strategy Pattern](#strategy-pattern)
    *   [Command Pattern](#command-pattern)
    *   [State Pattern](#state-pattern)
    *   [Mediator Pattern](#mediator-pattern)
6.  [Choosing the Right Pattern](#choosing-patterns)
7.  [Anti-Patterns to Be Aware Of](#anti-patterns)
8.  [Key Takeaways](#key-takeaways-design-patterns)

## 1. What are Design Patterns? <a name="what-are-design-patterns"></a>
Design patterns are formalized best practices that a programmer can use to solve common problems when designing an application or system. They represent well-tested solutions to recurring design challenges. They are not language-specific but can be implemented in any language, including JavaScript.

### Benefits of Using Design Patterns <a name="benefits-of-patterns"></a>
*   **Proven Solutions**: They are time-tested and well-understood solutions.
*   **Reusability**: Patterns provide a way to reuse existing designs and architectures.
*   **Common Vocabulary**: They provide a common language for developers to discuss solutions.
*   **Maintainability**: Code becomes more organized and easier to understand and maintain.
*   **Flexibility and Scalability**: Well-chosen patterns can make systems more flexible and easier to extend.

## 2. Categories of Design Patterns <a name="categories-of-patterns"></a>
Design patterns are typically categorized into three main groups based on their purpose:

### Creational Patterns <a name="creational-patterns"></a>
These patterns deal with object creation mechanisms, trying to create objects in a manner suitable to the situation. They increase flexibility and reuse of existing code.
*   Examples: Constructor, Factory, Singleton, Builder, Prototype.

### Structural Patterns <a name="structural-patterns"></a>
These patterns concern class and object composition. They explain how to assemble objects and classes into larger structures while keeping these structures flexible and efficient.
*   Examples: Adapter, Decorator, Facade, Proxy, Module, Bridge, Composite, Flyweight.

### Behavioral Patterns <a name="behavioral-patterns"></a>
These patterns are concerned with algorithms and the assignment of responsibilities between objects. They describe patterns of communication between objects.
*   Examples: Observer, Iterator, Strategy, Command, State, Mediator, Chain of Responsibility, Template Method, Visitor.

## 3. Common Creational Patterns in JavaScript <a name="common-creational"></a>

### Constructor Pattern <a name="constructor-pattern"></a>
Uses constructor functions (or ES6 classes) to create specific types of objects. The `new` keyword invokes the constructor.
```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  greet() {
    console.log(`Hello, I am ${this.name}.`);
  }
}
const alice = new Person("Alice", 30); // Object creation using constructor
alice.greet();
```
(Covered in [Tutorial 08](../08-js-objects-prototypes/README.md) and [Tutorial 18](../18-js-prototypes-inheritance-advanced/README.md))

### Factory Pattern <a name="factory-pattern"></a>
Provides an interface for creating objects in a superclass, but lets subclasses alter the type of objects that will be created. It encapsulates object creation logic.
```javascript
function createUser(type, name) {
  if (type === 'admin') {
    return { name, type, isAdmin: true, canEdit: true };
  }
  if (type === 'editor') {
    return { name, type, isAdmin: false, canEdit: true };
  }
  return { name, type, isAdmin: false, canEdit: false };
}

const admin = createUser('admin', 'Bob');
const editor = createUser('editor', 'Charlie');
console.log(admin);
console.log(editor);
```

### Singleton Pattern <a name="singleton-pattern"></a>
Ensures a class has only one instance and provides a global point of access to it.
```javascript
const Singleton = (function() {
  let instance;

  function createInstance() {
    const object = new Object("I am the instance");
    return object;
  }

  return {
    getInstance: function() {
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    }
  };
})();

const instance1 = Singleton.getInstance();
const instance2 = Singleton.getInstance();
console.log(instance1 === instance2); // true
```
Useful for managing shared resources like database connections or logging services.

### Prototype Pattern (Recap) <a name="prototype-pattern-recap"></a>
Creates new objects by cloning an existing object (the prototype). `Object.create()` is a direct implementation of this.
```javascript
const carPrototype = {
  wheels: 4,
  startEngine: function() { console.log('Engine started.'); }
};

const myCar = Object.create(carPrototype);
myCar.color = 'red';
console.log(myCar.wheels); // 4
myCar.startEngine();
```
(Covered in [Tutorial 08](../08-js-objects-prototypes/README.md) and [Tutorial 18](../18-js-prototypes-inheritance-advanced/README.md))

### Builder Pattern <a name="builder-pattern"></a>
Separates the construction of a complex object from its representation, allowing the same construction process to create various representations. Useful for objects with many optional parameters or configurations.
```javascript
class Burger {
  constructor(builder) {
    this.size = builder.size;
    this.cheese = builder.cheese || false;
    this.pepperoni = builder.pepperoni || false;
    this.lettuce = builder.lettuce || false;
    // ... other ingredients
  }
  display() {
    console.log(`Burger: Size ${this.size}, Cheese: ${this.cheese}, Pepperoni: ${this.pepperoni}, Lettuce: ${this.lettuce}`);
  }
}

class BurgerBuilder {
  constructor(size) {
    this.size = size;
  }
  addCheese() {
    this.cheese = true;
    return this; // For chaining
  }
  addPepperoni() {
    this.pepperoni = true;
    return this;
  }
  addLettuce() {
    this.lettuce = true;
    return this;
  }
  build() {
    return new Burger(this);
  }
}

const myBurger = new BurgerBuilder(10)
  .addCheese()
  .addLettuce()
  .build();
myBurger.display(); // Burger: Size 10, Cheese: true, Pepperoni: false, Lettuce: true
```

## 4. Common Structural Patterns in JavaScript <a name="common-structural"></a>

### Module Pattern (and ES6 Modules) <a name="module-pattern"></a>
Encapsulates a collection of methods and variables into a single unit (a module), controlling which parts are public and which remain private. ES6 Modules (`import`/`export`) are the standard way to achieve this now.
```javascript
// Pre-ES6 Module Pattern (using IIFE for private scope)
const MyModule = (function() {
  const _privateVar = 'secret';
  function _privateMethod() {
    console.log(_privateVar);
  }
  return {
    publicMethod: function() {
      _privateMethod();
      console.log('Public method called.');
    }
  };
})();
MyModule.publicMethod();
// MyModule._privateVar; // undefined
```
(ES6 Modules covered in [Tutorial 11](../11-js-modules/README.md))

### Adapter Pattern <a name="adapter-pattern"></a>
Allows objects with incompatible interfaces to collaborate. It acts as a wrapper that converts the interface of one object into an interface expected by the client.
```javascript
// Old API with specific method name
class OldCalculator {
  add(term1, term2) {
    return term1 + term2;
  }
}

// New API expects a different method name
class NewCalculator {
  sum(a, b) {
    return a + b;
  }
}

// Adapter
class CalculatorAdapter {
  constructor() {
    this.calculator = new NewCalculator();
  }
  add(term1, term2) {
    return this.calculator.sum(term1, term2); // Adapts 'add' to 'sum'
  }
}

const oldCalc = new OldCalculator();
console.log(oldCalc.add(5, 3)); // 8

const adaptedCalc = new CalculatorAdapter();
console.log(adaptedCalc.add(5, 3)); // 8 (uses NewCalculator internally)
```

### Decorator Pattern <a name="decorator-pattern"></a>
Dynamically adds new behavior or responsibilities to an object without altering its existing class or constructor function. It wraps the original object.
```javascript
class Coffee {
  cost() {
    return 5;
  }
  description() {
    return 'Simple coffee';
  }
}

// Decorator for Milk
function MilkDecorator(coffee) {
  const originalCost = coffee.cost();
  const originalDescription = coffee.description();

  coffee.cost = function() {
    return originalCost + 2;
  };
  coffee.description = function() {
    return originalDescription + ', with milk';
  };
  return coffee;
}

// Decorator for Sugar
function SugarDecorator(coffee) {
  const originalCost = coffee.cost();
  const originalDescription = coffee.description();

  coffee.cost = function() {
    return originalCost + 1;
  };
  coffee.description = function() {
    return originalDescription + ', with sugar';
  };
  return coffee;
}

let myCoffee = new Coffee();
myCoffee = MilkDecorator(myCoffee);
myCoffee = SugarDecorator(myCoffee);

console.log(myCoffee.cost()); // 8 (5 + 2 + 1)
console.log(myCoffee.description()); // Simple coffee, with milk, with sugar
```
(Note: JavaScript also has experimental decorator syntax for classes and methods, often used with transpilers like Babel or TypeScript.)

### Facade Pattern <a name="facade-pattern"></a>
Provides a simplified interface to a complex subsystem (a library, a framework, or any complex set of classes).
```javascript
// Complex subsystem parts
class SubsystemA {
  operationA() { console.log('Subsystem A operation'); }
}
class SubsystemB {
  operationB() { console.log('Subsystem B operation'); }
}

// Facade
class Facade {
  constructor() {
    this.subsystemA = new SubsystemA();
    this.subsystemB = new SubsystemB();
  }
  simplifiedOperation() {
    console.log('Facade: Simplified operation started...');
    this.subsystemA.operationA();
    this.subsystemB.operationB();
    console.log('Facade: Simplified operation finished.');
  }
}

const facade = new Facade();
facade.simplifiedOperation();
```

### Proxy Pattern <a name="proxy-pattern"></a>
Provides a surrogate or placeholder for another object to control access to it. ES6 introduced a built-in `Proxy` object.
```javascript
const target = {
  message1: "hello",
  message2: "everyone"
};

const handler = {
  get: function(targetObj, prop, receiver) {
    if (prop === "message2") {
      return "world"; // Modify access
    }
    return Reflect.get(...arguments);
  },
  set: function(targetObj, prop, value, receiver) {
    if (prop === "message1" && typeof value !== 'string') {
      throw new TypeError('Message1 must be a string');
    }
    return Reflect.set(...arguments);
  }
};

const proxy = new Proxy(target, handler);

console.log(proxy.message1); // "hello"
console.log(proxy.message2); // "world" (modified by proxy)

// proxy.message1 = 123; // TypeError: Message1 must be a string
proxy.message1 = "Hi";
console.log(proxy.message1); // "Hi"
```

## 5. Common Behavioral Patterns in JavaScript <a name="common-behavioral"></a>

### Observer Pattern (Publish/Subscribe) <a name="observer-pattern"></a>
Defines a one-to-many dependency between objects so that when one object (the subject or publisher) changes state, all its dependents (observers or subscribers) are notified and updated automatically.
```javascript
class Subject {
  constructor() {
    this.observers = [];
  }
  subscribe(observerFn) {
    this.observers.push(observerFn);
  }
  unsubscribe(observerFn) {
    this.observers = this.observers.filter(obs => obs !== observerFn);
  }
  notify(data) {
    this.observers.forEach(observerFn => observerFn(data));
  }
}

const subject = new Subject();

function observer1(data) { console.log(`Observer 1 received: ${data}`); }
function observer2(data) { console.log(`Observer 2 received: ${data}`); }

subject.subscribe(observer1);
subject.subscribe(observer2);

subject.notify("Hello Observers!");
// Observer 1 received: Hello Observers!
// Observer 2 received: Hello Observers!

subject.unsubscribe(observer1);
subject.notify("Data update!");
// Observer 2 received: Data update!
```
Common in event handling systems.

### Iterator Pattern (Recap) <a name="iterator-pattern-recap"></a>
Provides a way to access the elements of an aggregate object (collection) sequentially without exposing its underlying representation. JavaScript's built-in iterables (Arrays, Strings, Maps, Sets) and `for...of` loop use this pattern.
(Covered in [Tutorial 19: ES6+ Features Overview](../19-js-es6-features-summary/README.md))

### Strategy Pattern <a name="strategy-pattern"></a>
Defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients that use it.
```javascript
class Shipping {
  constructor() {
    this.company = null;
  }
  setStrategy(company) {
    this.company = company;
  }
  calculate(pkg) {
    if (!this.company) throw new Error('Shipping company not set');
    return this.company.calculate(pkg);
  }
}

// Strategies
class UPS {
  calculate(pkg) { return pkg.weight * 0.45; /* cost */ }
}
class FedEx {
  calculate(pkg) { return pkg.weight * 0.39; }
}

const shipping = new Shipping();
const pkg = { from: "A", to: "B", weight: 1.5 };

shipping.setStrategy(new UPS());
console.log("UPS Cost: " + shipping.calculate(pkg)); // UPS Cost: 0.675

shipping.setStrategy(new FedEx());
console.log("FedEx Cost: " + shipping.calculate(pkg)); // FedEx Cost: 0.585
```

### Command Pattern <a name="command-pattern"></a>
Encapsulates a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations.
```javascript
class CalculatorCommands {
  constructor() { this.currentValue = 0; this.commands = []; }
  execute(command) { this.currentValue = command.execute(this.currentValue); this.commands.push(command); }
  undo() { const cmd = this.commands.pop(); this.currentValue = cmd.undo(this.currentValue); }
}
class AddCommand { constructor(value) { this.valueToAdd = value; } execute(currentValue) { return currentValue + this.valueToAdd; } undo(currentValue) { return currentValue - this.valueToAdd; } }
class SubtractCommand { constructor(value) { this.valueToSubtract = value; } execute(currentValue) { return currentValue - this.valueToSubtract; } undo(currentValue) { return currentValue + this.valueToSubtract; } }

const calc = new CalculatorCommands();
calc.execute(new AddCommand(10));
calc.execute(new AddCommand(5));
console.log(calc.currentValue); // 15
calc.undo();
console.log(calc.currentValue); // 10
```

### State Pattern <a name="state-pattern"></a>
Allows an object to alter its behavior when its internal state changes. The object will appear to change its class.
```javascript
class TrafficLight {
  constructor() { this.state = new RedLightState(this); }
  changeState(state) { this.state = state; }
  signal() { this.state.signal(); }
}
class RedLightState { constructor(light) { this.light = light; } signal() { console.log("RED - Stop!"); setTimeout(() => this.light.changeState(new GreenLightState(this.light)), 2000); } }
class GreenLightState { constructor(light) { this.light = light; } signal() { console.log("GREEN - Go!"); setTimeout(() => this.light.changeState(new YellowLightState(this.light)), 2000); } }
class YellowLightState { constructor(light) { this.light = light; } signal() { console.log("YELLOW - Caution!"); setTimeout(() => this.light.changeState(new RedLightState(this.light)), 1000); } }

// const light = new TrafficLight();
// setInterval(() => light.signal(), 5000);
```

### Mediator Pattern <a name="mediator-pattern"></a>
Defines an object that encapsulates how a set of objects interact. Mediator promotes loose coupling by keeping objects from referring to each other explicitly, and it lets you vary their interaction independently.
```javascript
class ChatUser { constructor(name, mediator) { this.name = name; this.mediator = mediator; } send(message) { this.mediator.sendMessage(message, this); } receive(message, fromUser) { console.log(`${fromUser.name} to ${this.name}: ${message}`); } }
class ChatMediator { constructor() { this.users = []; } addUser(user) { this.users.push(user); } sendMessage(message, sender) { this.users.forEach(user => { if (user !== sender) { user.receive(message, sender); } }); } }

const mediator = new ChatMediator();
const user1 = new ChatUser("Alice", mediator);
const user2 = new ChatUser("Bob", mediator);
const user3 = new ChatUser("Charlie", mediator);
mediator.addUser(user1); mediator.addUser(user2); mediator.addUser(user3);

user1.send("Hi everyone!");
// Bob to Alice: Hi everyone! (Error in example, should be Alice to Bob/Charlie)
// Corrected logic: Message should be logged for recipients
// Bob receives: Alice to Bob: Hi everyone!
// Charlie receives: Alice to Charlie: Hi everyone!
```

## 6. Choosing the Right Pattern <a name="choosing-patterns"></a>
*   **Understand the Problem**: Clearly define the problem you're trying to solve.
*   **Consider Context**: The context of your application (size, complexity, team, performance needs) influences pattern choice.
*   **Know the Pattern's Intent**: Understand what each pattern is designed for, its benefits, and its drawbacks.
*   **Don't Overuse**: Avoid applying patterns just for the sake of it. Sometimes a simpler solution is better.
*   **Start Simple**: Implement the simplest solution first, and introduce patterns as the need for more structure or flexibility arises.

## 7. Anti-Patterns to Be Aware Of <a name="anti-patterns"></a>
Anti-patterns are common responses to recurring problems that are usually ineffective and may result in bad consequences. Examples include God Object (one class knows or does too much), Spaghetti Code, Lava Flow (dead code), etc. Recognizing these can help avoid common pitfalls.

## 8. Key Takeaways <a name="key-takeaways-design-patterns"></a>
*   Design patterns offer well-tested solutions to common software design problems.
*   They are categorized into Creational, Structural, and Behavioral patterns.
*   JavaScript's dynamic nature and support for first-class functions allow for flexible implementations of many patterns.
*   Understanding patterns improves code quality, maintainability, and communication among developers.
*   Choose patterns wisely based on the specific problem and context; don't force them.

---

⬅️ [Back to Best Practices & Coding Conventions](../20-js-best-practices/README.md) | ➡️ [Next Section: Functional Programming Concepts in JavaScript](../22-js-functional-programming/README.md) (Placeholder)
