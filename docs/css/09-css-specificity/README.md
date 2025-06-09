# 9. CSS Specificity

CSS Specificity is one of the most important concepts to understand when working with CSS. It's the set of rules that browsers use to determine which CSS property values are the most relevant to an element and, therefore, should be applied. When multiple CSS rules target the same element and try to set the same property, specificity decides which rule wins.

## Table of Contents
1.  [What is Specificity?](#what-is-specificity)
2.  [How is Specificity Calculated?](#how-calculated)
    *   [Inline Styles](#inline-styles)
    *   [IDs](#ids)
    *   [Classes, Attributes, and Pseudo-classes](#classes-attributes-pseudo-classes)
    *   [Elements and Pseudo-elements](#elements-pseudo-elements)
    *   [Universal Selector and Combinators](#universal-combinators)
3.  [The Specificity Hierarchy](#specificity-hierarchy)
4.  [The `!important` Rule](#important-rule)
5.  [Source Order](#source-order)
6.  [Inheritance and Specificity](#inheritance)
7.  [Practical Examples](#practical-examples)
8.  [Tools for Checking Specificity](#tools)
9.  [Best Practices for Managing Specificity](#best-practices)
10. [Key Takeaways](#key-takeaways)

## 1. What is Specificity? <a name="what-is-specificity"></a>

Specificity is a weight or rank that determines how CSS rules are applied by the browser. If two or more CSS rules point to the same element, the rule with the highest specificity value will be applied. Think of it as a scoring system.

## 2. How is Specificity Calculated? <a name="how-calculated"></a>

Browsers calculate specificity by assigning a weight to different types of selectors. This is often represented as a four-category value (e.g., `0,0,0,0`), although it's not strictly a base-10 calculation but rather a system where higher categories outweigh lower ones.

Let's break down the categories from highest to lowest precedence:

### a. Inline Styles <a name="inline-styles"></a>
*   **Specificity Value**: `1,0,0,0` (highest specificity)
*   Styles applied directly to an HTML element using the `style` attribute.
    ```html
    <p style="color: red;">This text will be red.</p>
    ```
    This rule (`color: red;`) has a specificity of `1,0,0,0`.

### b. IDs <a name="ids"></a>
*   **Specificity Value**: `0,1,0,0` for each ID selector.
*   Selectors that target an element using its unique `id` attribute (e.g., `#myElement`).
    ```css
    #myHeading {
      color: blue;
    }
    ```
    ```html
    <h1 id="myHeading">This heading will be blue.</h1>
    ```
    The selector `#myHeading` contributes `0,1,0,0` to the specificity.

### c. Classes, Attributes, and Pseudo-classes <a name="classes-attributes-pseudo-classes"></a>
*   **Specificity Value**: `0,0,1,0` for each class, attribute selector, or pseudo-class.
*   **Class selectors**: `.myClass`
*   **Attribute selectors**: `[type="text"]`, `[href*="example"]`
*   **Pseudo-classes**: `:hover`, `:focus`, `:nth-child()`, `:not()` (the specificity of `:not()` itself is 0, but the selectors inside it contribute to the overall specificity).
    ```css
    .highlight {
      color: green;
    }
    input[type="submit"] {
      background-color: orange;
    }
    a:hover {
      text-decoration: underline;
    }
    ```
    *   `.highlight` has `0,0,1,0`.
    *   `input[type="submit"]` has `0,0,1,1` (one attribute selector, one element selector).
    *   `a:hover` has `0,0,1,1` (one pseudo-class, one element selector).

### d. Elements and Pseudo-elements <a name="elements-pseudo-elements"></a>
*   **Specificity Value**: `0,0,0,1` for each element type selector or pseudo-element.
*   **Element type selectors**: `p`, `div`, `h1`
*   **Pseudo-elements**: `::before`, `::after`, `::first-line`
    ```css
    p {
      font-size: 16px;
    }
    div::before {
      content: "Prefix: ";
    }
    ```
    *   `p` has `0,0,0,1`.
    *   `div::before` has `0,0,0,2` (one element, one pseudo-element).

### e. Universal Selector and Combinators <a name="universal-combinators"></a>
*   The universal selector (`*`) and combinators (`+`, `>`, `~`, ` ` (descendant combinator)) have **no effect on specificity** (they count as `0,0,0,0`).

## 3. The Specificity Hierarchy <a name="specificity-hierarchy"></a>

When comparing two selectors, the browser goes through these categories from left to right (highest to lowest):
1.  **Inline Styles**: Wins over everything else (unless `!important` is used).
2.  **IDs**: An ID selector beats any number of class, attribute, or element selectors.
3.  **Classes, Attributes, Pseudo-classes**: One of these beats any number of element selectors.
4.  **Elements, Pseudo-elements**: The lowest on the totem pole.

**Example Comparison**:
*   `#nav .link` (Specificity: `0,1,1,0` - one ID, one class) beats `div ul li a` (Specificity: `0,0,0,4` - four elements).
*   `p.description` (Specificity: `0,0,1,1` - one class, one element) beats `footer p` (Specificity: `0,0,0,2` - two elements).

## 4. The `!important` Rule <a name="important-rule"></a>

Any declaration with `!important` appended to it will override **any other declaration**, regardless of specificity.

```css
p {
  color: blue !important; /* This will always make paragraphs blue */
}

#myParagraph {
  color: red; /* This will be overridden by the !important rule above */
}
```

*   **Use `!important` sparingly.** It can make debugging CSS very difficult because it breaks the natural cascading and specificity rules.
*   It's often a sign of poorly structured CSS or a quick fix that might cause problems later.
*   Legitimate uses include utility classes that must always apply (e.g., `.visually-hidden`) or user-defined stylesheets to override site styles for accessibility.

If two declarations with `!important` apply to the same element, the one with higher specificity wins. If they have the same specificity, the one that appears later in the CSS source order wins.

## 5. Source Order <a name="source-order"></a>

If two selectors have the **exact same specificity**, the rule that appears **later in the CSS source code** (or later in the order of linked stylesheets) will be applied.

```css
/* style.css */
p {
  color: blue;
}

p { /* Same specificity as the rule above */
  color: green; /* This rule wins because it's later in the source order */
}
```

## 6. Inheritance and Specificity <a name="inheritance"></a>

Inheritance is separate from specificity. Some CSS properties are inherited by child elements from their parents (e.g., `color`, `font-family`).
*   Directly applied styles (via a selector targeting the element) always win over inherited styles, regardless of the specificity of the parent's rule.
*   Specificity applies when multiple rules *directly target* the same element.

```css
body {
  color: blue; /* Specificity 0,0,0,1 - inherited by p */
}

p {
  /* No color property defined here */
}

.highlight {
  color: red !important; /* Specificity 1,0,1,0 (if !important used) or 0,0,1,0 */
}
```
```html
<body>
  <p>This paragraph will be blue (inherited from body).</p>
  <p class="highlight">This paragraph will be red (due to .highlight, overriding inheritance).</p>
</body>
```

## 7. Practical Examples <a name="practical-examples"></a>

Consider the following HTML:
```html
<div id="main-content">
  <p class="article-text important">This is a paragraph.</p>
</div>
```

And CSS:
```css
/* Rule 1 */
div#main-content p.article-text {
  color: blue; /* Specificity: 0,1,1,2 (1 ID, 1 class, 2 elements) */
}

/* Rule 2 */
#main-content .article-text {
  color: green; /* Specificity: 0,1,1,0 (1 ID, 1 class) */
}

/* Rule 3 */
p.important {
  color: red; /* Specificity: 0,0,1,1 (1 class, 1 element) */
}

/* Rule 4 */
.article-text {
    color: purple; /* Specificity: 0,0,1,0 (1 class) */
}
```

**Outcome**: The text will be **blue**.
*   Rule 1 (`0,1,1,2`) has higher specificity than Rule 2 (`0,1,1,0`), Rule 3 (`0,0,1,1`), and Rule 4 (`0,0,1,0`).

## 8. Tools for Checking Specificity <a name="tools"></a>

*   **Browser Developer Tools**: Most modern browsers (Chrome, Firefox, Edge, Safari) have developer tools that show you which CSS rules are applied to an element and often display the specificity of the selectors.
*   **Online Specificity Calculators**: There are many web-based tools where you can paste a selector and see its calculated specificity (e.g., [Keegan's Specificity Calculator](https://specificity.keegan.st/)).

## 9. Best Practices for Managing Specificity <a name="best-practices"></a>

*   **Keep selectors simple and low-specificity where possible**: This makes them easier to override and manage.
*   **Avoid overly qualified selectors**: Instead of `div#main-nav ul li a`, prefer `.main-nav-link` if possible.
*   **Rely on classes for styling**: IDs are very high-specificity and should be used sparingly for styling (often better for JavaScript hooks).
*   **Avoid `!important`**: Use it only as a last resort.
*   **Structure your CSS logically**: Well-organized CSS can help prevent specificity conflicts.
*   **Use CSS methodologies**: Frameworks like BEM (Block, Element, Modifier) can help manage specificity by promoting class-based, flat selectors.

## 10. Key Takeaways <a name="key-takeaways"></a>

*   Specificity determines which CSS rule applies when multiple rules target the same element.
*   It's calculated based on a hierarchy: Inline Styles > IDs > Classes/Attributes/Pseudo-classes > Elements/Pseudo-elements.
*   `!important` overrides all other declarations (use with extreme caution).
*   If specificities are equal, the later rule in the source order wins.
*   Directly applied styles beat inherited styles.
*   Understanding and managing specificity is crucial for writing maintainable and predictable CSS.

---

⬅️ [Back to CSS Positioning](../08-css-positioning/README.md) | ➡️ [Next Section: CSS Flexbox](../10-css-flexbox/README.md)
