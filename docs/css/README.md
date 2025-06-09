# 🎨 CSS Fundamentals: Styling the Web

Welcome to the CSS Fundamentals module! CSS (Cascading Style Sheets) is the language we use to style an HTML document. CSS describes how HTML elements should be displayed on screen, paper, or in other media. It controls the layout, colors, fonts, and overall visual appearance of web pages.

This tutorial series will guide you through the essential concepts of CSS, from basic syntax and selectors to advanced layout techniques like Flexbox and Grid, and ultimately, responsive web design.

## 📚 Module Outline

This CSS tutorial is broken down into the following sections. We recommend going through them in order:

1.  [**Introduction to CSS**](./01-introduction-to-css/README.md)
    *   What is CSS?
    *   CSS Syntax (Rules, Selectors, Declarations, Properties, Values)
    *   How to Add CSS to HTML (External, Internal, Inline)
    *   Basic Selectors (Element, Class, ID)
    *   CSS Comments

2.  [**CSS Selectors**](./02-css-selectors/README.md)
    *   Review of Basic Selectors
    *   Grouping Selectors
    *   Combinators (Descendant, Child, Adjacent Sibling, General Sibling)
    *   Attribute Selectors
    *   Pseudo-classes (e.g., `:hover`, `:focus`, `:nth-child`)
    *   Pseudo-elements (e.g., `::before`, `::after`, `::first-line`)
    *   The Universal Selector (`*`)

3.  [**The CSS Box Model**](./03-css-box-model/README.md)
    *   Understanding the Box Model: Content, Padding, Border, Margin
    *   `width` and `height` Properties
    *   `padding` Property (and its longhand forms)
    *   `border` Property (and its longhand forms: `border-width`, `border-style`, `border-color`)
    *   `margin` Property (and its longhand forms, margin collapsing)
    *   `box-sizing` Property (`content-box` vs. `border-box`)

4.  [**CSS Colors and Backgrounds**](./04-css-colors-and-backgrounds/README.md)
    *   The `color` Property
    *   CSS Color Values (Keywords, Hexadecimal, RGB, RGBA, HSL, HSLA)
    *   `background-color` Property
    *   `background-image` Property
    *   `background-repeat`, `background-position`, `background-attachment`, `background-size`
    *   Background Shorthand Property
    *   Gradients (Linear and Radial)

5.  [**CSS Typography**](./05-css-typography/README.md)
    *   `font-family` (Web Safe Fonts, Font Stacks, `@font-face`)
    *   `font-size`
    *   `font-weight`
    *   `font-style` (normal, italic, oblique)
    *   `text-decoration` (underline, overline, line-through)
    *   `text-align` (left, right, center, justify)
    *   `line-height`
    *   `letter-spacing` and `word-spacing`
    *   `text-transform` (uppercase, lowercase, capitalize)
    *   `text-shadow`

6.  [**CSS Units and Values**](./06-css-units-and-values/README.md)
    *   Absolute Units (e.g., `px`, `pt`, `cm`, `mm`, `in`)
    *   Relative Units (e.g., `em`, `rem`, `%`, `vw`, `vh`, `vmin`, `vmax`)
    *   When to Use Which Unit
    *   Color Units (revisited)
    *   Angle Units (e.g., `deg`, `rad`, `grad`, `turn` for transforms/gradients)

7.  [**The CSS `display` Property**](./07-css-display-property/README.md)
    *   Understanding `block`, `inline`, and `inline-block`
    *   The `none` value
    *   Introduction to `flex` and `grid` (as display values)
    *   Other `display` values (e.g., `table`, `list-item`)

8.  [**CSS Positioning**](./08-css-positioning/README.md)
    *   `position`: `static` (default)
    *   `position`: `relative`
    *   `position`: `absolute` (and containing blocks)
    *   `position`: `fixed`
    *   `position`: `sticky`
    *   `top`, `right`, `bottom`, `left` properties
    *   `z-index` for stacking order

9.  [**CSS Specificity, Cascade, and Inheritance**](./09-css-specificity-cascade-inheritance/README.md)
    *   The Cascade: How browsers resolve conflicting CSS rules
    *   Specificity: Calculating the weight of selectors (Inline, ID, Class/Attribute/Pseudo-class, Element/Pseudo-element)
    *   Inheritance: How some properties are passed down from parent to child elements
    *   The `!important` rule (and when to avoid it)
    *   Controlling Inheritance (`inherit`, `initial`, `unset`, `revert`)

10. [**CSS Flexbox**](./10-css-flexbox/README.md)
    *   Introduction to Flexible Box Layout
    *   Flex Container Properties (`display: flex`, `flex-direction`, `flex-wrap`, `flex-flow`, `justify-content`, `align-items`, `align-content`)
    *   Flex Item Properties (`order`, `flex-grow`, `flex-shrink`, `flex-basis`, `flex`, `align-self`)
    *   Practical Examples and Use Cases

11. [**CSS Grid Layout**](./11-css-grid-layout/README.md)
    *   Introduction to Grid Layout
    *   Grid Container Properties (`display: grid`, `grid-template-columns`, `grid-template-rows`, `grid-template-areas`, `grid-gap` / `gap`, `justify-items`, `align-items`, `justify-content`, `align-content`)
    *   Grid Item Properties (`grid-column-start`, `grid-column-end`, `grid-row-start`, `grid-row-end`, `grid-column`, `grid-row`, `grid-area`, `justify-self`, `align-self`)
    *   Practical Examples and Use Cases
    *   Combining Flexbox and Grid

12. [**Responsive Web Design with CSS & Media Queries**](./12-responsive-web-design/README.md)
    *   What is Responsive Web Design (RWD)?
    *   The Viewport Meta Tag (`<meta name="viewport">`)
    *   CSS Media Queries (Syntax, Logical Operators)
    *   Common Breakpoints
    *   Mobile-First vs. Desktop-First Approach
    *   Responsive Images and Typography
    *   Fluid Layouts vs. Fixed Layouts

13. [**CSS Transitions and Basic Animations**](./13-css-transitions-animations/README.md)
    *   CSS Transitions (`transition-property`, `transition-duration`, `transition-timing-function`, `transition-delay`, shorthand `transition`)
    *   CSS Animations (`@keyframes`, `animation-name`, `animation-duration`, `animation-timing-function`, `animation-delay`, `animation-iteration-count`, `animation-direction`, shorthand `animation`)
    *   Simple Use Cases for UI Enhancements

14. [**CSS Best Practices**](./14-css-best-practices/README.md)
    *   Organizing Your CSS (e.g., by component, feature)
    *   Naming Conventions (e.g., BEM, SMACSS - overview)
    *   Writing Readable and Maintainable CSS
    *   Commenting Your CSS
    *   Avoiding Over-Specificity and `!important`
    *   Using CSS Preprocessors (Brief introduction to Sass/LESS concepts)
    *   Performance Considerations (Minification, reducing unused CSS)

Let's begin by diving into the [Introduction to CSS](./01-introduction-to-css/README.md)!

---

⬅️ [Back to HTML Tutorials](../html/README.md) | ➡️ [Next Section: Introduction to CSS](./01-introduction-to-css/README.md)
