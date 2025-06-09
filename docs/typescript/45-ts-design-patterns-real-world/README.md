# 45. Design Patterns in TypeScript with Real-World Examples

Design patterns are reusable solutions to commonly occurring problems within a given context in software design. TypeScript, with its strong typing system, classes, interfaces, and other modern JavaScript features, provides an excellent environment for implementing these patterns in a clear, robust, and maintainable way.

This tutorial explores various design patterns, categorized by their intent (Creational, Structural, Behavioral), with TypeScript implementations and real-world examples.

## Table of Contents
1.  [Introduction to Design Patterns](#intro-design-patterns)
    *   What are Design Patterns?
    *   Benefits of using Design Patterns.
    *   How TypeScript enhances Design Pattern implementation.
2.  [Creational Patterns](#creational-patterns-ts)
    *   Focus: Object creation mechanisms, trying to create objects in a manner suitable to the situation.
    *   [**Singleton**](#singleton-ts)
        *   Ensures a class has only one instance and provides a global point of access to it.
        *   TypeScript implementation with `private constructor` and `static instance`.
        *   Real-world: Managing a global application configuration or a database connection pool.
    *   [**Factory Method**](#factory-method-ts)
        *   Defines an interface for creating an object, but lets subclasses alter the type of objects that will be created.
        *   TypeScript implementation using abstract classes/interfaces and concrete factories.
        *   Real-world: Document creation in an application (e.g., `TextDocumentFactory`, `SpreadsheetDocumentFactory`).
    *   [**Abstract Factory**](#abstract-factory-ts)
        *   Provides an interface for creating families of related or dependent objects without specifying their concrete classes.
        *   TypeScript implementation with interfaces for factories and products.
        *   Real-world: UI toolkit theming (e.g., `LightThemeFactory`, `DarkThemeFactory` creating themed buttons, inputs).
    *   [**Builder**](#builder-ts)
        *   Separates the construction of a complex object from its representation, allowing the same construction process to create different representations.
        *   TypeScript implementation with a `Builder` interface and concrete builders.
        *   Real-world: Constructing complex query objects or user profile configurations.
    *   [**Prototype**](#prototype-ts)
        *   Specifies the kinds of objects to create using a prototypical instance, and creates new objects by copying this prototype.
        *   TypeScript implementation with a `clone` method.
        *   Real-world: Creating copies of game objects or graphical elements.
3.  [Structural Patterns](#structural-patterns-ts)
    *   Focus: How classes and objects are composed to form larger structures.
    *   [**Adapter**](#adapter-ts)
        *   Allows objects with incompatible interfaces to collaborate.
        *   TypeScript implementation using a wrapper class.
        *   Real-world: Integrating a legacy API with a new system.
    *   [**Decorator**](#decorator-ts)
        *   Attaches additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.
        *   TypeScript implementation using class composition or TypeScript's decorator syntax.
        *   Real-world: Adding logging, caching, or compression to data sources or services.
    *   [**Facade**](#facade-ts)
        *   Provides a simplified interface to a library, a framework, or any other complex set of classes.
        *   TypeScript implementation with a class that delegates to subsystem components.
        *   Real-world: A simple API for a complex video conversion library.
    *   [**Composite**](#composite-ts)
        *   Composes objects into tree structures to represent part-whole hierarchies. Composite lets clients treat individual objects and compositions of objects uniformly.
        *   TypeScript implementation with a common interface for leaf and composite objects.
        *   Real-world: Representing a file system (files and directories) or UI component trees.
    *   [**Proxy**](#proxy-ts)
        *   Provides a surrogate or placeholder for another object to control access to it.
        *   TypeScript implementation for lazy loading, access control, or logging.
        *   Real-world: Lazy-loading images, caching remote service responses.
    *   [**Bridge**](#bridge-ts)
        *   Decouples an abstraction from its implementation so that the two can vary independently.
        *   TypeScript implementation using composition.
        *   Real-world: Separating UI components (e.g., a `Button`) from their platform-specific rendering logic (`WindowsButtonRenderer`, `MacButtonRenderer`).
4.  [Behavioral Patterns](#behavioral-patterns-ts)
    *   Focus: Algorithms and the assignment of responsibilities between objects.
    *   [**Strategy**](#strategy-ts)
        *   Defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients that use it.
        *   TypeScript implementation using interfaces for strategies and a context class.
        *   Real-world: Different payment methods in an e-commerce site, sorting algorithms.
    *   [**Observer**](#observer-ts)
        *   Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.
        *   TypeScript implementation with `Subject` and `Observer` interfaces.
        *   Real-world: Event handling systems, UI updates in response to data changes (e.g., Redux, RxJS).
    *   [**Command**](#command-ts)
        *   Turns a request into a stand-alone object that contains all information about the request. This transformation lets you parameterize methods with different requests, delay or queue a request's execution, and support undoable operations.
        *   TypeScript implementation with a `Command` interface and concrete command classes.
        *   Real-world: Implementing undo/redo functionality, task queues.
    *   [**Iterator**](#iterator-ts)
        *   Provides a way to access the elements of an aggregate object sequentially without exposing its underlying representation.
        *   TypeScript's built-in iterables and iterators (`Symbol.iterator`).
        *   Real-world: Traversing collections (arrays, maps, custom data structures).
    *   [**State**](#state-ts)
        *   Allows an object to alter its behavior when its internal state changes. The object will appear to change its class.
        *   TypeScript implementation using interfaces for states and a context class that holds the current state.
        *   Real-world: Managing the state of a document in an editor (Draft, Published, Archived), player states in a game.
    *   [**Mediator**](#mediator-ts)
        *   Defines an object that encapsulates how a set of objects interact. Mediator promotes loose coupling by keeping objects from referring to each other explicitly, and it lets you vary their interaction independently.
        *   TypeScript implementation with a `Mediator` interface and components communicating through it.
        *   Real-world: Chat room application, air traffic control system.
    *   [**Chain of Responsibility**](#chain-of-responsibility-ts)
        *   Avoids coupling the sender of a request to its receiver by giving more than one object a chance to handle the request. Chains the receiving objects and passes the request along the chain until an object handles it.
        *   TypeScript implementation with a handler interface and concrete handlers forming a chain.
        *   Real-world: Middleware in web frameworks (e.g., Express.js), event bubbling in UI.
    *   [**Template Method**](#template-method-ts)
        *   Defines the skeleton of an algorithm in an operation, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.
        *   TypeScript implementation with an abstract class containing the template method and abstract primitive operations.
        *   Real-world: Data processing pipelines, report generation.
5.  [TypeScript-Specific Patterns and Idioms](#ts-specific-patterns)
    *   Leveraging Utility Types (e.g., `Partial`, `Readonly`, `Pick`).
    *   Type Guards and Discriminated Unions for state management.
    *   Using ` 만족` operator for type checking without altering type.
    *   Branding/Nominal Typing (see Tutorial #31).
6.  [Conclusion and Further Learning](#conclusion-dp-ts)

## 1. Introduction to Design Patterns <a name="intro-design-patterns"></a>
Design patterns are time-tested, proven solutions to common problems in software design. They represent best practices evolved over time by experienced software developers. Using design patterns can make software more flexible, reusable, and maintainable.

TypeScript's features like static typing, interfaces, classes, generics, and access modifiers make implementing these patterns more explicit and safer, catching many errors at compile-time rather than runtime.

## 2. Creational Patterns <a name="creational-patterns-ts"></a>
These patterns deal with object creation mechanisms, trying to create objects in a manner suitable to the situation.

### Singleton <a name="singleton-ts"></a>
Ensures a class has only one instance and provides a global point of access to it.
```typescript
class AppConfig {
  private static instance: AppConfig;
  public readonly apiUrl: string;
  private readonly apiKey: string;

  private constructor() {
    // Simulate loading config
    this.apiUrl = process.env.API_URL || "https://api.example.com";
    this.apiKey = process.env.API_KEY || "default_secret_key";
    console.log("AppConfig initialized.");
  }

  public static getInstance(): AppConfig {
    if (!AppConfig.instance) {
      AppConfig.instance = new AppConfig();
    }
    return AppConfig.instance;
  }

  public getApiKey(): string {
    // Potentially hide direct access or add logic
    return this.apiKey;
  }
}

// Usage:
const config1 = AppConfig.getInstance();
const config2 = AppConfig.getInstance();

console.log(config1 === config2); // true
console.log(config1.apiUrl);
// console.log(config1.apiKey); // Property 'apiKey' is private
console.log(config1.getApiKey());
```
**Use Case:** Global configuration, logging service, database connection manager.

### Factory Method <a name="factory-method-ts"></a>
Defines an interface for creating an object, but lets subclasses decide which class to instantiate.
```typescript
interface ITransport {
  deliver(): void;
}

class Truck implements ITransport {
  public deliver(): void {
    console.log("Delivering by land in a truck.");
  }
}

class Ship implements ITransport {
  public deliver(): void {
    console.log("Delivering by sea in a ship.");
  }
}

abstract class Logistics {
  // The factory method
  public abstract createTransport(): ITransport;

  public planDelivery(): void {
    const transport = this.createTransport();
    console.log("Planning delivery...");
    transport.deliver();
  }
}

class RoadLogistics extends Logistics {
  public createTransport(): ITransport {
    return new Truck();
  }
}

class SeaLogistics extends Logistics {
  public createTransport(): ITransport {
    return new Ship();
  }
}

// Usage:
let logistics: Logistics;

logistics = new RoadLogistics();
logistics.planDelivery(); // Uses Truck

logistics = new SeaLogistics();
logistics.planDelivery(); // Uses Ship
```
**Use Case:** When a class cannot anticipate the class of objects it must create, or when a class wants its subclasses to specify the objects it creates.

### Abstract Factory <a name="abstract-factory-ts"></a>
Provides an interface for creating families of related or dependent objects without specifying their concrete classes.
```typescript
interface IButton {
  paint(): void;
}
interface ICheckbox {
  paint(): void;
}

class WindowsButton implements IButton {
  paint(): void { console.log("Painting a Windows button."); }
}
class MacButton implements IButton {
  paint(): void { console.log("Painting a macOS button."); }
}

class WindowsCheckbox implements ICheckbox {
  paint(): void { console.log("Painting a Windows checkbox."); }
}
class MacCheckbox implements ICheckbox {
  paint(): void { console.log("Painting a macOS checkbox."); }
}

interface IGUIFactory {
  createButton(): IButton;
  createCheckbox(): ICheckbox;
}

class WindowsFactory implements IGUIFactory {
  createButton(): IButton { return new WindowsButton(); }
  createCheckbox(): ICheckbox { return new WindowsCheckbox(); }
}

class MacFactory implements IGUIFactory {
  createButton(): IButton { return new MacButton(); }
  createCheckbox(): ICheckbox { return new MacCheckbox(); }
}

class Application {
  private button: IButton;
  private checkbox: ICheckbox;

  constructor(factory: IGUIFactory) {
    this.button = factory.createButton();
    this.checkbox = factory.createCheckbox();
  }

  paintUI(): void {
    this.button.paint();
    this.checkbox.paint();
  }
}

// Usage:
const os: string = "Windows"; // or "Mac"
let factory: IGUIFactory;

if (os === "Windows") {
  factory = new WindowsFactory();
} else {
  factory = new MacFactory();
}

const app = new Application(factory);
app.paintUI();
```
**Use Case:** Creating UI elements for different operating systems or themes.

### Builder <a name="builder-ts"></a>
Separates the construction of a complex object from its representation.
```typescript
class Pizza {
  public dough: string = "";
  public sauce: string = "";
  public toppings: string[] = [];

  public display(): void {
    console.log(`Pizza with ${this.dough} dough, ${this.sauce} sauce, and toppings: ${this.toppings.join(', ')}.`);
  }
}

interface IPizzaBuilder {
  reset(): void;
  setDough(dough: string): this;
  setSauce(sauce: string): this;
  addTopping(topping: string): this;
  getResult(): Pizza;
}

class MargheritaPizzaBuilder implements IPizzaBuilder {
  private pizza: Pizza;

  constructor() {
    this.pizza = new Pizza();
  }

  reset(): void {
    this.pizza = new Pizza();
  }

  setDough(dough: string): this {
    this.pizza.dough = dough;
    return this;
  }

  setSauce(sauce: string): this {
    this.pizza.sauce = sauce;
    return this;
  }

  addTopping(topping: string): this {
    this.pizza.toppings.push(topping);
    return this;
  }

  getResult(): Pizza {
    const result = this.pizza;
    this.reset(); // Important for future use of the builder instance
    return result;
  }
}

class PizzaDirector {
  private builder: IPizzaBuilder;

  constructor(builder: IPizzaBuilder) {
    this.builder = builder;
  }

  public changeBuilder(builder: IPizzaBuilder): void {
    this.builder = builder;
  }

  public makeMargherita(): Pizza {
    return this.builder
      .reset()
      .setDough("thin crust")
      .setSauce("tomato")
      .addTopping("mozzarella")
      .addTopping("basil")
      .getResult();
  }

  public makePepperoni(): Pizza {
     return this.builder
      .reset()
      .setDough("thick crust")
      .setSauce("spicy tomato")
      .addTopping("mozzarella")
      .addTopping("pepperoni")
      .getResult();
  }
}

// Usage:
const margheritaBuilder = new MargheritaPizzaBuilder();
const director = new PizzaDirector(margheritaBuilder);

const margherita = director.makeMargherita();
margherita.display();

const pepperoni = director.makePepperoni();
pepperoni.display();

// Or build step-by-step
const customPizza = margheritaBuilder
  .reset()
  .setDough("gluten-free")
  .setSauce("pesto")
  .addTopping("feta")
  .addTopping("olives")
  .getResult();
customPizza.display();
```
**Use Case:** Constructing complex objects like SQL queries, HTTP requests, or user configurations step-by-step.

## 3. Structural Patterns <a name="structural-patterns-ts"></a>
These patterns concern class and object composition. They explain how to assemble objects and classes into larger structures while keeping these structures flexible and efficient.

### Adapter <a name="adapter-ts"></a>
Allows objects with incompatible interfaces to collaborate.
```typescript
// Target interface expected by the client
interface INewPaymentProcessor {
  processPayment(amount: number): void;
}

// Adaptee: an existing class with an incompatible interface
class OldPaymentGateway {
  public makePayment(value: number, currency: string): void {
    console.log(`Processing ${currency}${value} payment via Old Gateway.`);
  }
}

// Adapter class
class PaymentAdapter implements INewPaymentProcessor {
  private adaptee: OldPaymentGateway;

  constructor(adaptee: OldPaymentGateway) {
    this.adaptee = adaptee;
  }

  public processPayment(amount: number): void {
    // Adapts the call to the adaptee's interface
    // Assume USD for simplicity
    this.adaptee.makePayment(amount, "USD");
  }
}

// Client code
function executePayment(processor: INewPaymentProcessor, amount: number) {
  processor.processPayment(amount);
}

// Usage:
const oldGateway = new OldPaymentGateway();
const adapter = new PaymentAdapter(oldGateway);

executePayment(adapter, 100);
```
**Use Case:** Integrating third-party libraries or legacy systems.

### Decorator <a name="decorator-ts"></a>
Attaches additional responsibilities to an object dynamically.
```typescript
interface Coffee {
  getCost(): number;
  getDescription(): string;
}

class SimpleCoffee implements Coffee {
  getCost(): number {
    return 10;
  }
  getDescription(): string {
    return "Simple coffee";
  }
}

abstract class CoffeeDecorator implements Coffee {
  protected decoratedCoffee: Coffee;

  constructor(coffee: Coffee) {
    this.decoratedCoffee = coffee;
  }

  getCost(): number {
    return this.decoratedCoffee.getCost();
  }

  getDescription(): string {
    return this.decoratedCoffee.getDescription();
  }
}

class MilkDecorator extends CoffeeDecorator {
  getCost(): number {
    return super.getCost() + 5;
  }
  getDescription(): string {
    return super.getDescription() + ", milk";
  }
}

class SugarDecorator extends CoffeeDecorator {
  getCost(): number {
    return super.getCost() + 2;
  }
  getDescription(): string {
    return super.getDescription() + ", sugar";
  }
}

// Usage:
let coffee: Coffee = new SimpleCoffee();
console.log(`${coffee.getDescription()} costs $${coffee.getCost()}`);

coffee = new MilkDecorator(coffee);
console.log(`${coffee.getDescription()} costs $${coffee.getCost()}`);

coffee = new SugarDecorator(coffee);
console.log(`${coffee.getDescription()} costs $${coffee.getCost()}`);

// TypeScript Decorators (syntax sugar for a similar concept)
function LogMethod(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function (...args: any[]) {
    console.log(`Calling ${propertyKey} with args: ${JSON.stringify(args)}`);
    const result = originalMethod.apply(this, args);
    console.log(`Method ${propertyKey} returned: ${JSON.stringify(result)}`);
    return result;
  };
  return descriptor;
}

class Calculator {
  @LogMethod
  add(a: number, b: number): number {
    return a + b;
  }
}

const calc = new Calculator();
calc.add(5, 3);
```
**Use Case:** Adding features like logging, caching, or access control to objects without modifying their class.

## 4. Behavioral Patterns <a name="behavioral-patterns-ts"></a>
These patterns are concerned with algorithms and the assignment of responsibilities between objects.

### Strategy <a name="strategy-ts"></a>
Defines a family of algorithms, encapsulates each one, and makes them interchangeable.
```typescript
interface ISortStrategy {
  sort<T>(data: T[]): T[];
}

class BubbleSortStrategy implements ISortStrategy {
  sort<T>(data: T[]): T[] {
    console.log("Sorting using Bubble Sort");
    // Naive implementation for demonstration
    return [...data].sort((a, b) => (a < b ? -1 : a > b ? 1 : 0)); // Simplified
  }
}

class QuickSortStrategy implements ISortStrategy {
  sort<T>(data: T[]): T[] {
    console.log("Sorting using Quick Sort");
    // Naive implementation for demonstration
    return [...data].sort((a, b) => (a < b ? -1 : a > b ? 1 : 0)); // Simplified
  }
}

class DataSorter {
  private strategy: ISortStrategy;

  constructor(strategy: ISortStrategy) {
    this.strategy = strategy;
  }

  public setStrategy(strategy: ISortStrategy): void {
    this.strategy = strategy;
  }

  public sortData<T>(data: T[]): T[] {
    return this.strategy.sort(data);
  }
}

// Usage:
const numbers = [5, 1, 4, 2, 8];

const bubbleSorter = new DataSorter(new BubbleSortStrategy());
bubbleSorter.sortData(numbers);

const quickSorter = new DataSorter(new QuickSortStrategy());
quickSorter.sortData(numbers);
```
**Use Case:** Payment processing with different gateways, data compression algorithms.

### Observer <a name="observer-ts"></a>
Defines a one-to-many dependency between objects.
```typescript
interface IObserver {
  update(data: any): void;
}

interface ISubject {
  attach(observer: IObserver): void;
  detach(observer: IObserver): void;
  notify(data: any): void;
}

class WeatherStation implements ISubject {
  private observers: IObserver[] = [];
  private temperature: number = 0;

  attach(observer: IObserver): void {
    const isExist = this.observers.includes(observer);
    if (isExist) {
      return console.log('Subject: Observer has been attached already.');
    }
    this.observers.push(observer);
    console.log('Subject: Attached an observer.');
  }

  detach(observer: IObserver): void {
    const observerIndex = this.observers.indexOf(observer);
    if (observerIndex === -1) {
      return console.log('Subject: Nonexistent observer.');
    }
    this.observers.splice(observerIndex, 1);
    console.log('Subject: Detached an observer.');
  }

  notify(data: any): void {
    console.log('Subject: Notifying observers...');
    for (const observer of this.observers) {
      observer.update(data);
    }
  }

  public setTemperature(temp: number): void {
    console.log(`WeatherStation: New temperature is ${temp}°C`);
    this.temperature = temp;
    this.notify({ temperature: this.temperature });
  }
}

class TemperatureDisplay implements IObserver {
  private name: string;
  constructor(name: string) { this.name = name; }
  update(data: { temperature: number }): void {
    console.log(`${this.name}: Temperature updated to ${data.temperature}°C`);
  }
}

class FanController implements IObserver {
  update(data: { temperature: number }): void {
    if (data.temperature > 25) {
      console.log("FanController: Temperature high, turning fan ON.");
    } else {
      console.log("FanController: Temperature normal, turning fan OFF.");
    }
  }
}

// Usage:
const weatherStation = new WeatherStation();

const display1 = new TemperatureDisplay("Display 1");
const fan = new FanController();

weatherStation.attach(display1);
weatherStation.attach(fan);

weatherStation.setTemperature(20);
weatherStation.setTemperature(30);

weatherStation.detach(display1);
weatherStation.setTemperature(22);
```
**Use Case:** Event systems, UI updates in response to model changes (e.g., in MVC/MVVM).

## 5. TypeScript-Specific Patterns and Idioms <a name="ts-specific-patterns"></a>
TypeScript's powerful type system enables certain patterns and idioms that are particularly effective:
*   **Type Guards and Discriminated Unions:** Excellent for state management and creating sum types (e.g., for API responses or error handling, see Tutorial #39).
*   **Utility Types (`Partial`, `Readonly`, `Pick`, `Omit`, etc.):** Transform existing types to create new ones, promoting DRY principles and type safety.
*   **`satisfies` Operator:** Check if an expression satisfies a type without changing its inferred type, useful for ensuring an object conforms to an interface while retaining its specific literal types.
*   **Branding/Nominal Typing:** Simulate nominal types for better type safety where structural typing isn't enough (see Tutorial #31).

## 6. Conclusion and Further Learning <a name="conclusion-dp-ts"></a>
Design patterns provide a shared vocabulary and proven solutions for common software design problems. TypeScript enhances their implementation by adding strong typing, which improves code clarity, reduces runtime errors, and facilitates easier refactoring.

To deepen your understanding:
*   Study the classic "Gang of Four" (GoF) book: "Design Patterns: Elements of Reusable Object-Oriented Software."
*   Explore pattern implementations in various TypeScript projects and libraries.
*   Practice applying these patterns to your own projects.

Understanding and using design patterns effectively is a hallmark of a mature software developer.

---

⬅️ [Back to TypeScript with WebAssembly (WASM) - In-Depth](../44-ts-wasm-deep-dive/README.md) | ➡️ [Next: Advanced TypeScript Configuration (tsconfig deep dive)](../46-ts-advanced-tsconfig/README.md) *(Placeholder)*
