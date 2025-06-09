# 00. Introduction to JavaScript

Welcome to the world of JavaScript! JavaScript (often abbreviated as JS) is a lightweight, interpreted, or just-in-time compiled programming language with first-class functions. While it is most well-known as the scripting language for Web pages, many non-browser environments also use it, thanks to Node.js.

JavaScript is a high-level, often just-in-time compiled, and multi-paradigm language. It has curly-bracket syntax, dynamic typing, prototype-based object-orientation, and first-class functions.

## Table of Contents
1.  [What is JavaScript?](#what-is-js)
2.  [A Brief History](#brief-history)
3.  [Why Learn JavaScript?](#why-learn-js)
    *   [Client-Side Web Development](#client-side)
    *   [Server-Side Development (Node.js)](#server-side)
    *   [Mobile App Development](#mobile-apps)
    *   [Desktop App Development](#desktop-apps)
    *   [Game Development](#game-dev)
4.  [How JavaScript Runs in a Browser](#how-js-runs)
    *   [The JavaScript Engine](#js-engine)
    *   [Interaction with HTML and CSS](#html-css-interaction)
5.  [Adding JavaScript to an HTML Page](#adding-js-to-html)
    *   [Inline JavaScript](#inline-js)
    *   [Internal JavaScript (within `<script>` tags)](#internal-js)
    *   [External JavaScript (linking a `.js` file)](#external-js)
    *   [Best Practice: External Files](#best-practice-external)
6.  [Basic Syntax Concepts](#basic-syntax)
    *   [Statements](#statements)
    *   [Comments (Single-line and Multi-line)](#comments-js)
    *   [Case Sensitivity](#case-sensitivity)
    *   [Whitespace](#whitespace-js)
7.  [Using the Browser Developer Console](#dev-console)
8.  ["Hello, World!" in JavaScript](#hello-world-js)
9.  [Key Takeaways](#key-takeaways-intro-js)

## 1. What is JavaScript? <a name="what-is-js"></a>
JavaScript is one of the three core technologies of the World Wide Web, alongside HTML and CSS.

*   **HTML (HyperText Markup Language)**: Provides the basic structure of sites, which is enhanced and modified by other technologies like CSS and JavaScript.
*   **CSS (Cascading Style Sheets)**: Used to control presentation, formatting, and layout (the look and feel).
*   **JavaScript**: Used to control the behavior of different elements and make web pages interactive. It can update and change both HTML and CSS.

Essentially, if HTML is the skeleton and CSS is the skin/clothing, then JavaScript is the brain and muscles, enabling movement and interaction.

## 2. A Brief History <a name="brief-history"></a>
*   Created by Brendan Eich at Netscape Communications in 1995.
*   Initially named Mocha, then LiveScript, and finally JavaScript (partly as a marketing tactic to leverage the popularity of Java at the time, though they are very different languages).
*   Standardized in 1997 as ECMAScript. JavaScript is the most well-known implementation of the ECMAScript standard.
*   Major updates to the ECMAScript standard (like ES5 in 2009, ES6/ES2015 in 2015, and annual releases thereafter) have significantly enhanced the language.

## 3. Why Learn JavaScript? <a name="why-learn-js"></a>
JavaScript is incredibly versatile and widely used:

### Client-Side Web Development <a name="client-side"></a>
This is its traditional role. JavaScript runs in the user's browser to:
*   Make web pages interactive (e.g., dropdown menus, image sliders, form validations).
*   Manipulate the Document Object Model (DOM) to dynamically change content and structure.
*   Communicate with servers asynchronously (AJAX) to fetch or send data without reloading the page.
*   Store data locally in the browser (localStorage, sessionStorage).

### Server-Side Development (Node.js) <a name="server-side"></a>
With Node.js, a runtime environment, JavaScript can run on servers to:
*   Build web servers and APIs.
*   Access databases.
*   Handle file systems.
*   Perform other server-side tasks.
This allows for full-stack development using a single language.

### Mobile App Development <a name="mobile-apps"></a>
Frameworks like React Native, Ionic, and NativeScript allow developers to build cross-platform mobile apps using JavaScript.

### Desktop App Development <a name="desktop-apps"></a>
Frameworks like Electron and NW.js enable the creation of cross-platform desktop applications using web technologies (HTML, CSS, and JavaScript).

### Game Development <a name="game-dev"></a>
Libraries and engines like Phaser, PixiJS, and even game engines like Unity (with UnityScript, though C# is more common now) support JavaScript for game development.

## 4. How JavaScript Runs in a Browser <a name="how-js-runs"></a>

### The JavaScript Engine <a name="js-engine"></a>
Every major web browser comes with a built-in JavaScript engine that executes JavaScript code. Some popular engines include:
*   **V8**: Google Chrome, Opera, Edge (Chromium-based)
*   **SpiderMonkey**: Firefox
*   **JavaScriptCore (Nitro)**: Safari

When a browser loads a web page, the HTML parser encounters JavaScript code (either embedded or linked). It then passes this code to the JavaScript engine, which interprets and executes it.

### Interaction with HTML and CSS <a name="html-css-interaction"></a>
JavaScript can interact with HTML and CSS in several ways:
*   **DOM Manipulation**: JavaScript can create, modify, and delete HTML elements and their attributes. The DOM (Document Object Model) is a programming interface for HTML documents, representing the page so that programs can change the document structure, style, and content.
*   **CSS Manipulation**: JavaScript can change CSS styles dynamically (e.g., changing an element's color, size, or position).
*   **Event Handling**: JavaScript can respond to user actions (like clicks, mouse movements, key presses) and browser events (like page load, window resize).

## 5. Adding JavaScript to an HTML Page <a name="adding-js-to-html"></a>
There are three main ways to include JavaScript in an HTML file:

### Inline JavaScript <a name="inline-js"></a>
JavaScript code can be placed directly within HTML event attributes, like `onclick` or `onmouseover`. This is generally discouraged for larger scripts as it mixes structure and behavior.

```html
<button onclick="alert('Hello from inline JavaScript!');">Click Me</button>
```

### Internal JavaScript (within `<script>` tags) <a name="internal-js"></a>
JavaScript code can be embedded directly within `<script>` tags in the HTML document, usually placed in the `<head>` or just before the closing `</body>` tag.

```html
<!DOCTYPE html>
<html>
<head>
  <title>Internal JS</title>
</head>
<body>
  <h1>My Web Page</h1>
  <script>
    alert('Hello from internal JavaScript!');
    console.log('This message is for the console.');
  </script>
</body>
</html>
```
Placing scripts before the closing `</body>` tag is often preferred because it allows the HTML content to be parsed and displayed to the user first, improving perceived page load speed. If a script in the `<head>` tries to access HTML elements that haven't been loaded yet, it can cause errors (unless deferred or specifically handled).

### External JavaScript (linking a `.js` file) <a name="external-js"></a>
This is the most common and recommended method. JavaScript code is written in a separate file with a `.js` extension, and then linked to the HTML document using the `src` attribute of the `<script>` tag.

**`myScript.js`:**
```javascript
// This is myScript.js
alert('Hello from external JavaScript!');
console.log('External script loaded.');
```

**`index.html`:**
```html
<!DOCTYPE html>
<html>
<head>
  <title>External JS</title>
</head>
<body>
  <h1>My Web Page</h1>
  <script src="myScript.js"></script> <!-- Linking the external file -->
</body>
</html>
```

### Best Practice: External Files <a name="best-practice-external"></a>
Using external `.js` files offers several advantages:
*   **Separation of Concerns**: Keeps HTML structure and JavaScript behavior separate, making code cleaner and easier to manage.
*   **Reusability**: The same script file can be used across multiple HTML pages.
*   **Maintainability**: Easier to update and debug JavaScript code when it's in its own file.
*   **Caching**: Browsers can cache external `.js` files, leading to faster page loads on subsequent visits.

## 6. Basic Syntax Concepts <a name="basic-syntax"></a>

### Statements <a name="statements"></a>
JavaScript programs consist of statements. Each statement is an instruction for the computer to perform an action. Statements are typically separated by semicolons (`;`). While semicolons are sometimes optional due to Automatic Semicolon Insertion (ASI), it's a good practice to use them to avoid ambiguity.

```javascript
let message = "Hello, world!"; // A statement assigning a string to a variable
console.log(message);          // A statement calling the console.log function
```

### Comments (Single-line and Multi-line) <a name="comments-js"></a>
Comments are used to explain code and make it more understandable. They are ignored by the JavaScript engine.

*   **Single-line comments**: Start with `//`
    ```javascript
    // This is a single-line comment
    let x = 10; // This comment is at the end of a line
    ```
*   **Multi-line comments**: Start with `/*` and end with `*/`
    ```javascript
    /*
      This is a
      multi-line comment.
      It can span several lines.
    */
    let y = 20;
    ```

### Case Sensitivity <a name="case-sensitivity"></a>
JavaScript is case-sensitive. This means that variables, function names, and keywords must be typed with consistent capitalization.

```javascript
let myVariable = 10;
let myvariable = 20;

console.log(myVariable); // Outputs: 10
console.log(myvariable); // Outputs: 20 (This is a different variable)

// Keywords like 'let', 'const', 'function' must be lowercase.
// LET x = 5; // This would cause an error.
```

### Whitespace <a name="whitespace-js"></a>
JavaScript largely ignores extra whitespace (spaces, tabs, newlines) that is not part of a string. This allows you to format your code for better readability.

```javascript
let name   =   "Alice"; // Valid
let age=30;             // Valid, but less readable

// Good formatting:
let city = "New York";
let country = "USA";
```

## 7. Using the Browser Developer Console <a name="dev-console"></a>
The browser's developer console is an indispensable tool for JavaScript development. You can open it in most browsers by right-clicking on a webpage and selecting "Inspect" or "Inspect Element," then navigating to the "Console" tab (or by pressing F12).

The console allows you to:
*   **View `console.log()` output**: Display messages, variable values, and debugging information from your scripts.
*   **See errors and warnings**: JavaScript errors and browser warnings are reported here.
*   **Execute JavaScript code directly**: You can type and run JavaScript commands interactively.
*   **Inspect the DOM and CSS** (in other tabs of the developer tools).

```javascript
console.log("This message will appear in the developer console.");
let userAge = 25;
console.log("User's age:", userAge);
```

## 8. "Hello, World!" in JavaScript <a name="hello-world-js"></a>
Let's create a simple HTML file with an external JavaScript file to display "Hello, World!".

**`index.html`:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hello JavaScript!</title>
</head>
<body>
  <h1>My First JavaScript Program</h1>
  <p id="greeting"></p> <!-- We'll put our message here -->

  <script src="app.js"></script>
</body>
</html>
```

**`app.js`:**
```javascript
// app.js

// Method 1: Display an alert box
// alert("Hello, World!");

// Method 2: Write to the HTML document (generally not recommended for dynamic updates after page load)
// document.write("Hello, World!");

// Method 3: Write to the browser console
console.log("Hello, World! from the console.");

// Method 4: Modify an HTML element's content
// This is a common way to display dynamic content.
// We select the paragraph with the id 'greeting' and change its text.
document.getElementById("greeting").textContent = "Hello, World! JavaScript is running!";
```
To see this in action, save both files in the same folder, then open `index.html` in your web browser. You should see the text on the page change, and a message in the developer console.

## 9. Key Takeaways <a name="key-takeaways-intro-js"></a>
*   JavaScript is a versatile programming language primarily used for making web pages interactive.
*   It can also be used for server-side development (Node.js), mobile apps, desktop apps, and more.
*   JavaScript runs in the browser via a JavaScript engine and can manipulate HTML (DOM) and CSS.
*   It's best practice to write JavaScript in external `.js` files and link them to your HTML.
*   Basic syntax includes statements, comments, and case sensitivity.
*   The browser developer console is a crucial tool for debugging and testing JavaScript.

This introduction provides a foundation for your JavaScript journey. In the upcoming tutorials, we'll dive deeper into variables, data types, operators, control structures, functions, objects, and much more.

---

⬅️ [Back to CSS Methodologies](../../css/17-css-methodologies/README.md) | ➡️ [Next Section: JavaScript Variables and Data Types](../01-js-variables-datatypes/README.md)
