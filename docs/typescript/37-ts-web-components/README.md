# 37. TypeScript with Web Components

Web Components are a set of web platform APIs that allow you to create custom, reusable, encapsulated HTML tags to use in web pages and web apps. Using TypeScript with Web Components can greatly enhance the development experience by providing type safety for properties, attributes, events, and methods, leading to more robust and maintainable components.

This tutorial explores how to build Web Components with TypeScript, covering the core concepts and popular tools.

## Table of Contents
1.  [Introduction to Web Components](#intro-web-components)
    *   Custom Elements
    *   Shadow DOM
    *   HTML Templates (`<template>` and `<slot>`)
    *   ES Modules (for packaging and distribution)
2.  [Why TypeScript for Web Components?](#why-ts-web-components)
    *   Type safety for properties and attributes
    *   Strongly-typed events
    *   Clearer component APIs
    *   Improved refactoring and maintainability
3.  [Creating Custom Elements with TypeScript](#creating-custom-elements-ts)
    *   Extending `HTMLElement`
    *   Defining the custom element: `customElements.define()`
    *   Lifecycle Callbacks: `connectedCallback`, `disconnectedCallback`, `adoptedCallback`, `attributeChangedCallback`
4.  [Typing Properties and Attributes](#typing-props-attributes-ts)
    *   Distinction between JavaScript properties and HTML attributes
    *   Using getters and setters for properties
    *   `static get observedAttributes()` and `attributeChangedCallback()`
    *   Reflecting properties to attributes (and vice-versa) type-safely
5.  [Handling Events Type-Safely](#handling-events-ts)
    *   Dispatching `CustomEvent` with typed `detail`
    *   Listening to DOM events and custom events with proper typings
6.  [Using Shadow DOM with TypeScript](#shadow-dom-ts)
    *   Attaching a Shadow DOM: `this.attachShadow({ mode: 'open' | 'closed' })`
    *   Accessing `this.shadowRoot` (and its type)
    *   Styling encapsulated components (`<style>` within Shadow DOM)
    *   Working with `<slot>` elements
7.  [Decorators for Web Components (with Libraries)](#decorators-web-components-ts)
    *   How decorators can simplify property/attribute definitions, event handling, etc.
    *   Examples from libraries like Lit or Stencil
8.  [Popular Libraries for Building Web Components with TypeScript](#popular-libraries-ts-wc)
    *   [**Lit (formerly LitElement & lit-html)**](#lit-ts)
        *   Simple, fast, and expressive library by Google
        *   Uses tagged template literals for rendering
        *   Strong TypeScript support with decorators (`@customElement`, `@property`, `@state`, `@eventOptions`)
    *   [**StencilJS**](#stenciljs-ts)
        *   A compiler that generates standards-compliant Web Components
        *   Uses JSX for templating and has a component model similar to frameworks
        *   Strongly typed with decorators (`@Component`, `@Prop`, `@State`, `@Event`, `@Listen`)
    *   [**Hybrids.js**](#hybrids-ts)
        *   Functional and declarative approach with a focus on plain objects and functions.
    *   [**Vanilla TypeScript (No Library)**](#vanilla-ts-wc)
        *   Understanding the fundamentals by building without abstractions
9.  [Data Binding and State Management](#data-binding-state-wc-ts)
    *   Implementing simple data binding within a component
    *   Strategies for managing more complex state (internal state, or integrating with external stores)
10. [Testing TypeScript-based Web Components](#testing-ts-wc)
    *   Unit testing component logic
    *   Testing rendering and interaction (e.g., with Testing Library or Web Component Tester)
    *   Mocking dependencies
11. [Integrating with Frameworks (React, Angular, Vue)](#integrating-frameworks-wc-ts)
    *   How major frameworks can consume standard Web Components
    *   Passing data (properties/attributes) and handling events
    *   Type considerations when wrapping or using Web Components in frameworks
12. [Best Practices](#best-practices-ts-wc)
13. [Key Takeaways](#key-takeaways-ts-wc)

## 1. Introduction to Web Components <a name="intro-web-components"></a>
Web Components are based on four main specifications:
*   **Custom Elements:** APIs for defining new HTML tags and their behavior.
*   **Shadow DOM:** APIs for encapsulated DOM and styling. Styles and scripts within a Shadow DOM don't leak out, and external styles don't leak in (by default).
*   **HTML Templates:** The `<template>` and `<slot>` elements allow you to define inert chunks of markup to be cloned and used later, with slots for content projection.
*   **ES Modules:** Define how to include and reuse JS documents in a standards-based, modular, performant way.

## 2. Why TypeScript for Web Components? <a name="why-ts-web-components"></a>
*   **Type Safety for Properties/Attributes:** Ensure data passed to your components is of the correct type.
*   **Strongly-Typed Events:** Define precise shapes for `CustomEvent` details, making event handling less error-prone.
*   **Clearer Component APIs:** TypeScript interfaces and types make the public API of your components explicit and easier to understand.
*   **Improved Maintainability:** Refactoring component internals or their APIs is safer with compile-time checks.

## 3. Creating Custom Elements with TypeScript <a name="creating-custom-elements-ts"></a>
```typescript
class MyCounter extends HTMLElement {
  private count: number = 0;
  private countSpan!: HTMLSpanElement; // Definite assignment assertion

  constructor() {
    super(); // Always call super first in constructor
    // It's generally recommended to do DOM work in connectedCallback
  }

  connectedCallback() {
    // Called when the element is added to the document's DOM
    this.attachShadow({ mode: 'open' });

    const wrapper = document.createElement('div');
    this.countSpan = document.createElement('span');
    this.countSpan.textContent = this.count.toString();

    const incrementButton = document.createElement('button');
    incrementButton.textContent = 'Increment';
    incrementButton.addEventListener('click', this.increment.bind(this));

    wrapper.appendChild(this.countSpan);
    wrapper.appendChild(incrementButton);
    this.shadowRoot!.appendChild(wrapper); // Non-null assertion for shadowRoot
  }

  disconnectedCallback() {
    // Called when the element is removed from the document's DOM
    // Clean up event listeners, etc.
  }

  adoptedCallback() {
    // Called when the element is moved to a new document
  }

  private increment() {
    this.count++;
    this.countSpan.textContent = this.count.toString();
    this.dispatchEvent(new CustomEvent('countChanged', { detail: { count: this.count } }));
  }
}

customElements.define('my-counter', MyCounter);
```

## 4. Typing Properties and Attributes <a name="typing-props-attributes-ts"></a>
HTML attributes are always strings. JavaScript properties can be any type.
```typescript
class UserProfile extends HTMLElement {
  private _userId: string | null = null;
  private _isAdmin: boolean = false;

  static get observedAttributes() {
    return ['user-id', 'is-admin']; // Attributes to monitor for changes
  }

  constructor() { super(); }

  connectedCallback() {
    this.render();
  }

  attributeChangedCallback(name: string, oldValue: string | null, newValue: string | null) {
    console.log(`Attribute ${name} changed from ${oldValue} to ${newValue}`);
    switch (name) {
      case 'user-id':
        this._userId = newValue;
        break;
      case 'is-admin':
        this._isAdmin = newValue !== null && newValue !== 'false'; // Attribute to boolean
        break;
    }
    this.render(); // Re-render when attributes change
  }

  get userId(): string | null {
    return this._userId;
  }
  set userId(value: string | null) {
    if (this._userId !== value) {
      this._userId = value;
      if (value) {
        this.setAttribute('user-id', value);
      } else {
        this.removeAttribute('user-id');
      }
      this.render();
    }
  }

  get isAdmin(): boolean {
    return this._isAdmin;
  }
  set isAdmin(value: boolean) {
    if (this._isAdmin !== value) {
      this._isAdmin = value;
      this.setAttribute('is-admin', value.toString());
      this.render();
    }
  }

  private render() {
    // Simplified render logic
    this.innerHTML = `User ID: ${this.userId}, Admin: ${this.isAdmin}`;
  }
}
customElements.define('user-profile', UserProfile);
```

## 5. Handling Events Type-Safely <a name="handling-events-ts"></a>
```typescript
interface MyCustomEventDetail {
  message: string;
  value: number;
}

// Dispatching an event
const myEvent = new CustomEvent<MyCustomEventDetail>('my-custom-event', {
  detail: {
    message: 'Hello from custom element!',
    value: 42
  },
  bubbles: true,
  composed: true // Allows event to cross Shadow DOM boundary
});
this.dispatchEvent(myEvent);

// Listening to an event
this.addEventListener('my-custom-event', (event: Event) => {
  // Type assertion needed if accessing detail directly on Event
  const customEvent = event as CustomEvent<MyCustomEventDetail>;
  console.log(customEvent.detail.message); // 'Hello from custom element!'
  console.log(customEvent.detail.value);   // 42
});
```

## 6. Using Shadow DOM with TypeScript <a name="shadow-dom-ts"></a>
Shadow DOM provides encapsulation for DOM and CSS.
```typescript
class MyStyledComponent extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' }); // 'open' allows access via this.shadowRoot
  }

  connectedCallback() {
    if (!this.shadowRoot) return; // Type guard for shadowRoot

    const style = document.createElement('style');
    style.textContent = `
      :host { /* Styles for the custom element itself */
        display: block;
        border: 1px solid blue;
        padding: 10px;
      }
      p {
        color: green;
      }
      ::slotted(span) { /* Styles for slotted content */
        font-weight: bold;
      }
    `;

    const content = document.createElement('p');
    content.textContent = 'I am a styled web component!';
    
    const slot = document.createElement('slot'); // Default slot
    slot.name = 'myslot'; // Named slot

    this.shadowRoot.appendChild(style);
    this.shadowRoot.appendChild(content);
    this.shadowRoot.appendChild(slot);
  }
}
customElements.define('my-styled-component', MyStyledComponent);

// Usage: <my-styled-component><span slot="myslot">Slotted!</span></my-styled-component>
```

## 7. Decorators for Web Components (with Libraries) <a name="decorators-web-components-ts"></a>
Libraries like Lit and Stencil heavily use decorators to reduce boilerplate.
```typescript
// Example using Lit decorators (conceptual)
import { LitElement, html, customElement, property } from 'lit-element';

@customElement('simple-greeting')
export class SimpleGreeting extends LitElement {
  @property({ type: String })
  name = 'World';

  render() {
    return html`<p>Hello, ${this.name}!</p>`;
  }
}
```
TypeScript needs `experimentalDecorators` and `emitDecoratorMetadata` enabled in `tsconfig.json` for this style (though Lit's decorators work without `emitDecoratorMetadata`).

## 8. Popular Libraries for Building Web Components with TypeScript <a name="popular-libraries-ts-wc"></a>

### Lit (formerly LitElement & lit-html) <a name="lit-ts"></a>
*   Lightweight, fast, and uses tagged template literals (`lit-html`) for efficient rendering.
*   Excellent TypeScript integration with decorators for properties, state, and custom element registration.

### StencilJS <a name="stenciljs-ts"></a>
*   A compiler that generates standards-compliant Web Components. Components can be used standalone or within frameworks.
*   Uses JSX for templating and a class-based component model with decorators.
*   Provides features like pre-rendering, lazy loading, and optimized builds.

### Hybrids.js <a name="hybrids-ts"></a>
*   Offers a more functional and declarative API using plain objects and factory functions rather than classes.
*   Good TypeScript support.

### Vanilla TypeScript (No Library) <a name="vanilla-ts-wc"></a>
*   Building directly with platform APIs. Gives maximum control and understanding but involves more boilerplate.

## 9. Data Binding and State Management <a name="data-binding-state-wc-ts"></a>
*   **Simple Binding:** Re-render the component (or parts of it) when properties/attributes change.
*   **Internal State:** Manage component-specific state within the class. Libraries like Lit provide reactive properties (`@state` decorator) that trigger re-renders on change.
*   **External Stores:** For complex applications, Web Components can subscribe to external state management stores (Redux, Zustand, etc.) like any other JavaScript module.

## 10. Testing TypeScript-based Web Components <a name="testing-ts-wc"></a>
*   **Unit Tests (Jest, Mocha):** Test component logic, methods, and event dispatching.
*   **DOM Interaction/Rendering Tests:** Use tools like `@testing-library/dom`, `@open-wc/testing`, or Playwright/Puppeteer for end-to-end or integration tests that involve rendering and user interaction.
*   Ensure `customElements.define` has been called before trying to create/interact with the element in tests.

## 11. Integrating with Frameworks (React, Angular, Vue) <a name="integrating-frameworks-wc-ts"></a>
Most modern JavaScript frameworks can consume standard Web Components.
*   **React:** Treats them like regular HTML elements. Pass data via attributes (string-only for primitive data) or properties (for complex data, requires refs). Event handling uses standard `addEventListener` or framework-specific wrappers.
*   **Angular:** Can use Web Components directly in templates. Use property binding for complex data and event binding for custom events. May need `CUSTOM_ELEMENTS_SCHEMA`.
*   **Vue:** Similar to React, can use them as native HTML elements. Use `:prop-name` for property binding and `@event-name` for event listening.
*   **Type Safety:** When wrapping Web Components in a framework component, you can provide TypeScript typings for the wrapper that match the Web Component's API.

## 12. Best Practices <a name="best-practices-ts-wc"></a>
*   **Keep APIs Simple:** Design clear and concise property, attribute, and event APIs.
*   **Encapsulation:** Use Shadow DOM for style and DOM encapsulation unless there's a strong reason not to.
*   **Accessibility (A11y):** Ensure components are accessible by using ARIA attributes and proper semantic HTML internally.
*   **Performance:** Be mindful of work done in `connectedCallback` and `attributeChangedCallback`. Defer expensive operations if possible.
*   **Use Libraries Wisely:** Libraries like Lit or Stencil can significantly reduce boilerplate and provide optimizations, but understand the underlying Web Component APIs too.

## 13. Key Takeaways <a name="key-takeaways-ts-wc"></a>
*   TypeScript significantly enhances Web Component development by adding type safety and improving code quality.
*   Understanding core Web Component APIs (Custom Elements, Shadow DOM, Templates) is crucial, even when using libraries.
*   Typed properties, attributes, and events lead to more robust and predictable components.
*   Libraries like Lit and Stencil offer powerful abstractions and tooling for building Web Components with TypeScript.
*   Web Components built with TypeScript can be integrated into various JavaScript frameworks, promoting reusability.

By combining the power of Web Components standards with the safety of TypeScript, you can create truly interoperable, maintainable, and scalable UI components.

---

⬅️ [Back to TypeScript with Microfrontends](../36-ts-microfrontends/README.md) | ➡️ [Next: Type-Safe Data Fetching and API Communication in TypeScript](../38-ts-api-communication/README.md) *(Placeholder)*
