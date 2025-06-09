# 2. CSS Selectors

Selectors are one of the most fundamental concepts in CSS. They are patterns that allow you to select and target specific HTML elements to apply styles to. Mastering selectors is key to effectively controlling the appearance of your web pages.

In the previous section, we introduced basic selectors (element, class, and ID). This section will delve deeper into these and introduce more advanced selectors and combinators.

## Table of Contents
1.  [Review of Basic Selectors](#review-basic)
    *   [Element Selector (Type Selector)](#element-selector-review)
    *   [Class Selector](#class-selector-review)
    *   [ID Selector](#id-selector-review)
2.  [Grouping Selector](#grouping-selector)
3.  [Combinators](#combinators)
    *   [Descendant Combinator (space)](#descendant-combinator)
    *   [Child Combinator (`>`)](#child-combinator)
    *   [Adjacent Sibling Combinator (`+`)](#adjacent-sibling-combinator)
    *   [General Sibling Combinator (`~`)](#general-sibling-combinator)
4.  [Attribute Selectors](#attribute-selectors)
    *   [`[attribute]`](#attr-exists)
    *   [`[attribute=value]`](#attr-exact-value)
    *   [`[attribute~=value]`](#attr-contains-word)
    *   [`[attribute|=value]`](#attr-starts-hyphen)
    *   [`[attribute^=value]`](#attr-starts-with)
    *   [`[attribute$=value]`](#attr-ends-with)
    *   [`[attribute*=value]`](#attr-contains-substring)
    *   [Case Insensitivity (`i`)](#attr-case-insensitive)
5.  [Pseudo-classes](#pseudo-classes)
    *   [Link Pseudo-classes (`:link`, `:visited`)](#link-pseudo)
    *   [User Action Pseudo-classes (`:hover`, `:active`, `:focus`)](#user-action-pseudo)
    *   [Input Pseudo-classes (`:enabled`, `:disabled`, `:checked`, `:required`, `:optional`, etc.)](#input-pseudo)
    *   [Structural Pseudo-classes (`:root`, `:empty`, `:first-child`, `:last-child`, `:nth-child()`, `:nth-of-type()`, etc.)](#structural-pseudo)
    *   [Negation Pseudo-class (`:not()`)](#not-pseudo)
6.  [Pseudo-elements](#pseudo-elements)
    *   [`::before`](#before-pseudo-element)
    *   [`::after`](#after-pseudo-element)
    *   [`::first-letter`](#first-letter-pseudo-element)
    *   [`::first-line`](#first-line-pseudo-element)
    *   [`::selection`](#selection-pseudo-element)
    *   [`::placeholder`](#placeholder-pseudo-element)
7.  [The Universal Selector (`*`)](#universal-selector)
8.  [Selector Specificity (Brief Overview)](#specificity-overview)
9.  [Key Takeaways](#key-takeaways)

## 1. Review of Basic Selectors <a name="review-basic"></a>

Let's quickly recap the selectors covered in the introduction:

### Element Selector (Type Selector) <a name="element-selector-review"></a>
*   Selects all instances of a given HTML element.
    ```css
    p { color: gray; }
    h2 { font-family: 'Georgia', serif; }
    ```

### Class Selector <a name="class-selector-review"></a>
*   Selects all elements with a specific `class` attribute.
*   Starts with a period (`.`).
    ```css
    .highlight { background-color: yellow; }
    .text-center { text-align: center; }
    ```
    ```html
    <p class="highlight">This text is highlighted.</p>
    ```

### ID Selector <a name="id-selector-review"></a>
*   Selects a single element with a specific `id` attribute.
*   Starts with a hash (`#`).
*   IDs must be unique per page.
    ```css
    #main-navigation { list-style-type: none; }
    ```
    ```html
    <ul id="main-navigation">
        <li><a href="/">Home</a></li>
    </ul>
    ```

## 2. Grouping Selector <a name="grouping-selector"></a>

*   You can apply the same styles to multiple selectors by listing them, separated by commas.
*   This helps reduce code duplication.

```css
h1, h2, h3 {
  font-family: 'Arial', sans-serif;
  color: #333;
}

.button, .widget, #sidebar {
  border: 1px solid #ccc;
}
```

## 3. Combinators <a name="combinators"></a>

Combinators allow you to select elements based on their relationship to other elements.

### Descendant Combinator (space) <a name="descendant-combinator"></a>
*   Selects elements that are descendants (nested anywhere inside) of another element.
*   Represented by a space between selectors.

```css
/* Selects all <p> elements that are inside a <div> */
div p {
  margin-bottom: 10px;
}

/* Selects all <a> elements inside an element with class 'nav-menu' */
.nav-menu a {
  text-decoration: none;
}
```

### Child Combinator (`>`) <a name="child-combinator"></a>
*   Selects elements that are direct children of another element.
*   Represented by a `>` character between selectors.

```css
/* Selects all <li> elements that are direct children of a <ul> */
ul > li {
  list-style-position: inside;
}

/* Selects <p> elements that are direct children of an <article> element */
article > p {
  font-style: italic;
}
```
**Difference between Descendant and Child:**
```html
<div>
    <p>This paragraph is a direct child of div (and a descendant).</p>
    <span>
        <p>This paragraph is a descendant of div, but NOT a direct child.</p>
    </span>
</div>
```
```css
div p { color: blue; }    /* Both paragraphs will be blue */
div > p { font-weight: bold; } /* Only the first paragraph will be bold */
```

### Adjacent Sibling Combinator (`+`) <a name="adjacent-sibling-combinator"></a>
*   Selects an element that is immediately preceded by a specific element, and both are children of the same parent.
*   Represented by a `+` character between selectors.

```css
/* Selects a <p> element that immediately follows an <h1> element */
h1 + p {
  margin-top: 0;
  font-size: 1.2em;
}

/* Selects a <ul> that immediately follows an element with class 'intro' */
.intro + ul {
  border-top: 1px solid silver;
}
```

### General Sibling Combinator (`~`) <a name="general-sibling-combinator"></a>
*   Selects all elements that are siblings of a specified element and appear after it in the HTML structure (they share the same parent).
*   Represented by a `~` character between selectors.

```css
/* Selects all <p> elements that are siblings of and come after an <h1> */
h1 ~ p {
  color: #555;
}

/* Selects all <img> elements that are siblings of and come after an element with id 'logo' */
#logo ~ img {
  opacity: 0.8;
}
```

## 4. Attribute Selectors <a name="attribute-selectors"></a>

Attribute selectors allow you to select elements based on the presence or value of their attributes.

### `[attribute]` <a name="attr-exists"></a>
*   Selects elements that have the specified attribute, regardless of its value.
    ```css
    /* Selects all <a> elements that have a 'title' attribute */
    a[title] {
      cursor: help;
    }
    /* Selects all elements that have a 'disabled' attribute */
    [disabled] {
      opacity: 0.5;
      cursor: not-allowed;
    }
    ```

### `[attribute=value]` <a name="attr-exact-value"></a>
*   Selects elements that have the specified attribute with an exact value.
    ```css
    /* Selects all <input> elements with type="text" */
    input[type="text"] {
      border: 1px solid #ccc;
    }
    /* Selects all <a> elements with href="#top" */
    a[href="#top"] {
      font-weight: bold;
    }
    ```

### `[attribute~=value]` <a name="attr-contains-word"></a>
*   Selects elements whose attribute value is a space-separated list of words, one of which is exactly `value`.
    ```css
    /* Selects elements with class attribute containing 'important' as a whole word */
    [class~="important"] {
      color: red;
    }
    /* <p class="highlight important featured"> matches */
    /* <p class="veryimportant"> does NOT match */
    ```

### `[attribute|=value]` <a name="attr-starts-hyphen"></a>
*   Selects elements whose attribute value is exactly `value` or starts with `value` immediately followed by a hyphen (`-`). Often used for language subcodes.
    ```css
    /* Selects elements with a 'lang' attribute of 'en' or 'en-US', 'en-GB', etc. */
    [lang|="en"] {
      font-style: italic;
    }
    ```

### `[attribute^=value]` <a name="attr-starts-with"></a>
*   Selects elements whose attribute value **begins** with the specified `value`.
    ```css
    /* Selects <a> elements whose href starts with "https://" */
    a[href^="https://"] {
      background: url('secure-icon.png') no-repeat left center;
      padding-left: 18px;
    }
    ```

### `[attribute$=value]` <a name="attr-ends-with"></a>
*   Selects elements whose attribute value **ends** with the specified `value`.
    ```css
    /* Selects <a> elements whose href ends with ".pdf" */
    a[href$=".pdf"] {
      font-weight: bold;
      padding-right: 20px;
      background: url('pdf-icon.png') no-repeat right center;
    }
    ```

### `[attribute*=value]` <a name="attr-contains-substring"></a>
*   Selects elements whose attribute value **contains** the specified `value` as a substring.
    ```css
    /* Selects <a> elements whose href contains "example.com" */
    a[href*="example.com"] {
      color: green;
    }
    ```

### Case Insensitivity (`i`) <a name="attr-case-insensitive"></a>
*   You can add ` i` before the closing bracket `]` in attribute selectors to make the value matching case-insensitive (where supported by the attribute and browser).
    ```css
    /* Selects input elements with type="text" regardless of case (e.g., TEXT, Text) */
    input[type="text" i] {
      border-color: blue;
    }
    ```

## 5. Pseudo-classes <a name="pseudo-classes"></a>

Pseudo-classes select elements based on a special state or characteristic that isn't directly present in the document tree (like an attribute).
They are keywords added to selectors that specify a special state of the selected element(s). They start with a colon (`:`).

### Link Pseudo-classes <a name="link-pseudo"></a>
*   `:link`: Selects unvisited links.
*   `:visited`: Selects visited links.
    ```css
    a:link { color: blue; }
    a:visited { color: purple; }
    ```
    *Note: For privacy reasons, browsers limit the styles that can be applied to `:visited` links.*

### User Action Pseudo-classes <a name="user-action-pseudo"></a>
*   `:hover`: Selects an element when the user mouses over it.
*   `:active`: Selects an element when it is being activated (e.g., clicked).
*   `:focus`: Selects an element when it has keyboard focus (e.g., clicked on or tabbed to an input field or link).
    ```css
    button:hover { background-color: lightgray; }
    button:active { background-color: gray; }
    input:focus { border-color: dodgerblue; outline: none; }
    ```
    *LVHA Order*: It's common to define these in the order Link-Visited-Hover-Active (`:link`, `:visited`, `:hover`, `:active`) for links to ensure they work correctly due to specificity.

### Input Pseudo-classes <a name="input-pseudo"></a>
*   Related to form elements:
    *   `:enabled`: Selects enabled input elements.
    *   `:disabled`: Selects disabled input elements.
    *   `:checked`: Selects checked checkboxes or radio buttons.
    *   `:required`: Selects input fields with the `required` attribute.
    *   `:optional`: Selects input fields without the `required` attribute.
    *   `:in-range`, `:out-of-range`: For inputs with min/max values.
    *   `:valid`, `:invalid`: Based on HTML5 form validation.
    ```css
    input[type="text"]:disabled { background-color: #eee; }
    input[type="checkbox"]:checked + label { font-weight: bold; }
    input:required { border-left: 3px solid red; }
    ```

### Structural Pseudo-classes <a name="structural-pseudo"></a>
*   Select elements based on their position in the document tree:
    *   `:root`: Selects the root element of the document (usually `<html>`).
    *   `:empty`: Selects elements that have no children (including text nodes).
    *   `:first-child`: Selects an element that is the first child of its parent.
    *   `:last-child`: Selects an element that is the last child of its parent.
    *   `:only-child`: Selects an element that is the only child of its parent.
    *   `:nth-child(n)`: Selects elements based on their position among siblings. `n` can be a number, keyword (`odd`, `even`), or a formula (`an+b`).
        *   `li:nth-child(3)`: Selects the third `<li>`.
        *   `li:nth-child(odd)`: Selects odd `<li>` elements (1st, 3rd, 5th, etc.).
        *   `li:nth-child(2n)`: Selects even `<li>` elements (2nd, 4th, 6th, etc. - same as `even`).
        *   `li:nth-child(3n+1)`: Selects the 1st, 4th, 7th, etc. `<li>`.
    *   `:nth-last-child(n)`: Same as `:nth-child(n)` but counts from the end.
    *   `:first-of-type`: Selects the first sibling of its type.
    *   `:last-of-type`: Selects the last sibling of its type.
    *   `:only-of-type`: Selects an element that is the only sibling of its type.
    *   `:nth-of-type(n)`: Selects siblings of a certain type based on their position among siblings of the same type.
    ```css
    li:first-child { font-weight: bold; }
    tr:nth-child(even) { background-color: #f2f2f2; } /* Zebra striping for tables */
    p:nth-of-type(1) { text-indent: 2em; }
    ```

### Negation Pseudo-class (`:not()`) <a name="not-pseudo"></a>
*   Selects elements that do **not** match the selector passed into `:not()`.
    ```css
    /* Selects all <p> elements that do not have the class 'special' */
    p:not(.special) {
      color: #333;
    }

    /* Selects all input fields that are not disabled */
    input:not([disabled]) {
      border: 1px solid green;
    }
    ```

## 6. Pseudo-elements <a name="pseudo-elements"></a>

Pseudo-elements style a specific part of an element, rather than the element itself. They start with a double colon (`::`) in modern CSS (though single colon `:` is supported for older pseudo-elements for backward compatibility with CSS2).

### `::before` <a name="before-pseudo-element"></a>
*   Creates a pseudo-element that is the first child of the selected element. Often used to insert cosmetic content using the `content` property.

### `::after` <a name="after-pseudo-element"></a>
*   Creates a pseudo-element that is the last child of the selected element. Also often used with the `content` property.
    ```css
    .important-note::before {
      content: "Note: ";
      font-weight: bold;
    }

    a[href^="http://"]::after {
      content: " (external link)";
      font-size: 0.8em;
    }
    ```
    *The `content` property is required for `::before` and `::after` to render.*

### `::first-letter` <a name="first-letter-pseudo-element"></a>
*   Selects the first letter of the first line of a block-level element.
    ```css
    p::first-letter {
      font-size: 2em;
      font-weight: bold;
      color: #8A2BE2; /* BlueViolet */
      float: left;
      margin-right: 0.1em;
    }
    ```

### `::first-line` <a name="first-line-pseudo-element"></a>
*   Selects the first line of a block-level element.
    ```css
    article p::first-line {
      text-transform: uppercase;
      letter-spacing: 1px;
    }
    ```

### `::selection` <a name="selection-pseudo-element"></a>
*   Applies styles to the portion of a document that has been highlighted (selected) by the user.
    ```css
    ::selection {
      background-color: #ffb7b7; /* Light pink */
      color: #000;
    }
    ```

### `::placeholder` <a name="placeholder-pseudo-element"></a>
*   Styles the placeholder text in an `<input>` or `<textarea>` element.
    ```css
    input::placeholder {
      color: #aaa;
      font-style: italic;
    }
    ```

## 7. The Universal Selector (`*`) <a name="universal-selector"></a>

*   Selects all elements on the page.
*   It has low specificity.
*   Often used for resets or to apply a global style (though this should be done with caution due to performance implications and specificity).

```css
/* Apply box-sizing: border-box to all elements (common practice) */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

/* Selects all elements within an element with class 'container' */
.container * {
  border: 1px dotted lightgray;
}
```

## 8. Selector Specificity (Brief Overview) <a name="specificity-overview"></a>

Specificity is a weight that is applied to a given CSS declaration, determined by the number of each selector type in the matching selector. When multiple declarations have equal specificity, the last declaration found in the CSS is applied.

Generally, the hierarchy is:
1.  Inline styles (highest specificity)
2.  ID selectors
3.  Class selectors, attribute selectors, and pseudo-classes
4.  Element selectors (type selectors) and pseudo-elements (lowest specificity)

Understanding specificity is crucial for debugging why certain styles are not being applied. This will be covered in more detail in a later section.

## Key Takeaways <a name="key-takeaways"></a>

*   Selectors are patterns to target HTML elements.
*   **Grouping selectors** (`,`) apply styles to multiple selectors at once.
*   **Combinators** (space, `>`, `+`, `~`) select elements based on their relationships.
*   **Attribute selectors** (`[]`) target elements based on their attributes and values.
*   **Pseudo-classes** (`:`) select elements in a specific state (e.g., `:hover`, `:nth-child()`).
*   **Pseudo-elements** (`::`) style specific parts of an element (e.g., `::before`, `::first-letter`).
*   The **Universal selector** (`*`) selects all elements.
*   Be mindful of selector specificity when writing CSS.

Mastering these selectors will give you fine-grained control over styling your web pages.

---

⬅️ [Back to Introduction to CSS](../01-introduction-to-css/README.md) | ➡️ [Next Section: The CSS Box Model](../03-css-box-model/README.md)
