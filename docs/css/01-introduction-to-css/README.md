# 1. Introduction to CSS

Welcome to the world of CSS! CSS, which stands for Cascading Style Sheets, is a cornerstone technology of the World Wide Web, alongside HTML and JavaScript. While HTML provides the structure for web content, CSS is responsible for its presentation – how it looks, feels, and is laid out.

## Table of Contents
1.  [What is CSS?](#what-is-css)
2.  [Why Use CSS?](#why-use-css)
3.  [CSS Syntax](#css-syntax)
    *   [Ruleset](#ruleset)
    *   [Selector](#selector)
    *   [Declaration Block](#declaration-block)
    *   [Properties and Values](#properties-and-values)
4.  [How to Add CSS to HTML](#how-to-add-css)
    *   [External CSS](#external-css)
    *   [Internal CSS](#internal-css)
    *   [Inline CSS](#inline-css)
    *   [Which Method to Choose?](#which-method)
5.  [Basic Selectors](#basic-selectors)
    *   [Element Selector (Type Selector)](#element-selector)
    *   [Class Selector](#class-selector)
    *   [ID Selector](#id-selector)
6.  [CSS Comments](#css-comments)
7.  [Key Takeaways](#key-takeaways)

## What is CSS? <a name="what-is-css"></a>

CSS (Cascading Style Sheets) is a style sheet language used for describing the presentation of a document written in a markup language such as HTML or XML. CSS describes how elements should be rendered on screen, on paper, in speech, or on other media.

Think of HTML as the skeleton of a webpage (the structure and content) and CSS as the clothing and appearance (the colors, fonts, layout, etc.).

## Why Use CSS? <a name="why-use-css"></a>

*   **Separation of Concerns**: CSS allows you to separate the content (HTML) from the presentation (CSS). This makes your code cleaner, easier to maintain, and more organized.
*   **Consistency**: You can define styles once and apply them to multiple HTML elements across many pages, ensuring a consistent look and feel for your website.
*   **Maintainability**: When you need to change the appearance of your website, you can often do so by editing a few CSS files, rather than modifying every single HTML page.
*   **Accessibility**: Well-structured CSS can improve accessibility by allowing users to customize the presentation of content (e.g., increasing font size).
*   **Device Compatibility (Responsiveness)**: CSS is crucial for creating responsive web designs that adapt to different screen sizes and devices (desktops, tablets, phones).
*   **Richer Styling**: CSS offers a vast array of properties to control layout, colors, fonts, spacing, animations, and much more, far beyond what HTML attributes alone can achieve.

## CSS Syntax <a name="css-syntax"></a>

A CSS rule is made up of a selector and a declaration block.

### Ruleset <a name="ruleset"></a>
The entire structure of a selector plus a declaration block is called a **ruleset** (or often just a "rule").

```css
/* This is a CSS ruleset */
selector {
  property: value;
}
```

### Selector <a name="selector"></a>
*   The **selector** points to the HTML element(s) you want to style.
*   Examples: `h1`, `.my-class`, `#my-id`

### Declaration Block <a name="declaration-block"></a>
*   The **declaration block** is enclosed in curly braces `{}`.
*   It contains one or more declarations separated by semicolons.

### Properties and Values <a name="properties-and-values"></a>
*   Each **declaration** consists of a CSS **property** name and a **value**, separated by a colon.
    *   **Property**: The style attribute you want to change (e.g., `color`, `font-size`, `background-color`).
    *   **Value**: The specific setting for that property (e.g., `blue`, `16px`, `#FFFFFF`).

**Example:**

```css
h1 { /* Selector: targets all <h1> elements */
  color: blue;      /* Declaration: property 'color', value 'blue' */
  font-size: 24px;  /* Declaration: property 'font-size', value '24px' */
} /* End of declaration block */

p { /* Selector: targets all <p> elements */
  color: green;
  line-height: 1.5;
}
```

## How to Add CSS to HTML <a name="how-to-add-css"></a>

There are three primary ways to include CSS styles in an HTML document:

### 1. External CSS <a name="external-css"></a>
*   Styles are defined in a separate `.css` file (e.g., `styles.css`).
*   This file is then linked to the HTML document using the `<link>` tag within the `<head>` section.
*   **This is the most common and recommended method.**

**HTML (`index.html`):**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Styled Page</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <h1>Hello World!</h1>
    <p>This page is styled with an external CSS file.</p>
</body>
</html>
```

**CSS (`styles.css`):**
```css
body {
  font-family: Arial, sans-serif;
}

h1 {
  color: navy;
}

p {
  color: gray;
}
```

### 2. Internal CSS (Embedded CSS) <a name="internal-css"></a>
*   CSS rules are placed directly within the HTML document inside a `<style>` tag, typically within the `<head>` section.
*   Useful for single-page websites or for styles specific to one page.

**HTML (`index.html`):**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Styled Page</title>
    <style>
        body {
          font-family: Verdana, sans-serif;
        }

        h1 {
          color: green;
          text-align: center;
        }

        p {
          color: darkolivegreen;
        }
    </style>
</head>
<body>
    <h1>Hello World!</h1>
    <p>This page is styled with internal CSS.</p>
</body>
</html>
```

### 3. Inline CSS <a name="inline-css"></a>
*   CSS rules are applied directly to individual HTML elements using the `style` attribute.
*   This method has the highest specificity and will override external and internal styles.
*   **Generally discouraged for extensive styling** because it mixes content with presentation, making maintenance harder and reducing the benefits of CSS. Use sparingly for very specific, isolated style changes.

**HTML (`index.html`):**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Styled Page</title>
</head>
<body>
    <h1 style="color: purple; text-align: right;">Hello World!</h1>
    <p style="background-color: lightyellow; font-style: italic;">This paragraph is styled with inline CSS.</p>
</body>
</html>
```

### Which Method to Choose? <a name="which-method"></a>
*   **External CSS**: Best for most cases, especially for multi-page websites. Promotes clean separation of concerns, reusability, and easier maintenance.
*   **Internal CSS**: Suitable for single HTML documents or when styles are unique to a specific page and you want to keep everything in one file. Can also be useful for quickly testing styles.
*   **Inline CSS**: Use sparingly for very specific, targeted style overrides that cannot be easily achieved with external or internal styles, or for dynamically applying styles with JavaScript. Overuse leads to unmaintainable code.

## Basic Selectors <a name="basic-selectors"></a>

Selectors are patterns that match against elements in an HTML document. Here are the most fundamental ones:

### Element Selector (Type Selector) <a name="element-selector"></a>
*   Selects all HTML elements of a specific type (e.g., `p`, `h1`, `div`).
    ```css
    p {
      font-size: 16px;
    }
    div {
      border: 1px solid black;
    }
    ```

### Class Selector <a name="class-selector"></a>
*   Selects all elements that have a specific `class` attribute.
*   Denoted by a period (`.`) followed by the class name.
*   An element can have multiple classes.
    ```html
    <p class="highlight">This paragraph is highlighted.</p>
    <div class="highlight important">This div is highlighted and important.</div>
    ```
    ```css
    .highlight {
      background-color: yellow;
    }
    .important {
      font-weight: bold;
    }
    ```

### ID Selector <a name="id-selector"></a>
*   Selects a single element that has a specific `id` attribute.
*   Denoted by a hash (`#`) followed by the ID name.
*   **An `id` must be unique within an HTML document.**
    ```html
    <div id="main-header">Site Title</div>
    ```
    ```css
    #main-header {
      font-size: 32px;
      color: teal;
    }
    ```
*   **Note**: While powerful, IDs have high specificity, which can sometimes make overriding styles more difficult. Use classes for general styling and IDs for unique page landmarks or JavaScript hooks.

## CSS Comments <a name="css-comments"></a>
*   Comments in CSS are used to explain your code and are ignored by the browser.
*   They start with `/*` and end with `*/`.
*   Comments can span multiple lines.

```css
/* This is a single-line comment */

h1 {
  color: navy; /* Set the color for main headings */
}

/*
  This is a
  multi-line comment
  explaining the styles for paragraphs.
*/
p {
  font-size: 1em;
  line-height: 1.6;
}
```

## Key Takeaways <a name="key-takeaways"></a>
*   CSS is used to style the presentation and layout of HTML documents.
*   It promotes separation of concerns, consistency, and maintainability.
*   A CSS ruleset consists of a selector and a declaration block (properties and values).
*   CSS can be added to HTML externally (linked `.css` file - recommended), internally (`<style>` tag), or inline (`style` attribute).
*   Basic selectors include element selectors (e.g., `h1`), class selectors (e.g., `.my-class`), and ID selectors (e.g., `#my-id`).
*   Comments (`/* ... */`) are used to document CSS code.

This introduction provides a foundational understanding of CSS. In the upcoming sections, we'll delve deeper into selectors, the box model, colors, typography, and much more.

---

⬅️ [Back to CSS Tutorial Home](../README.md) | ➡️ [Next Section: CSS Selectors](../02-css-selectors/README.md)
