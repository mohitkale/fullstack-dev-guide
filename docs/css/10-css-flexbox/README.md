# 10. CSS Flexbox (Flexible Box Layout)

CSS Flexbox, or the Flexible Box Layout Module, is a one-dimensional layout model designed to provide a more efficient way to lay out, align, and distribute space among items in a container, even when their size is unknown or dynamic. It's a fundamental part of modern CSS for creating responsive and complex UIs.

## Table of Contents
1.  [Introduction to Flexbox](#intro-flexbox)
    *   [Why Flexbox?](#why-flexbox)
    *   [Main Axis and Cross Axis](#main-cross-axis)
2.  [Flex Container Properties](#flex-container-properties)
    *   [`display: flex` or `display: inline-flex`](#display-flex)
    *   [`flex-direction`](#flex-direction)
    *   [`flex-wrap`](#flex-wrap)
    *   [`flex-flow` (shorthand for `flex-direction` and `flex-wrap`)](#flex-flow)
    *   [`justify-content`](#justify-content)
    *   [`align-items`](#align-items)
    *   [`align-content`](#align-content)
    *   [`gap`, `row-gap`, `column-gap`](#gap)
3.  [Flex Item Properties](#flex-item-properties)
    *   [`order`](#order)
    *   [`flex-grow`](#flex-grow)
    *   [`flex-shrink`](#flex-shrink)
    *   [`flex-basis`](#flex-basis)
    *   [`flex` (shorthand for `flex-grow`, `flex-shrink`, and `flex-basis`)](#flex-shorthand)
    *   [`align-self`](#align-self)
4.  [Common Use Cases](#common-use-cases)
    *   Navigation Bars
    *   Card Layouts
    *   Centering Elements (Horizontally and Vertically)
    *   Distributing Space
5.  [Flexbox vs. CSS Grid](#flexbox-vs-grid)
6.  [Browser Support and Prefixes](#browser-support)
7.  [Key Takeaways](#key-takeaways)

## 1. Introduction to Flexbox <a name="intro-flexbox"></a>

Flexbox allows you to design complex layouts with relative ease. The main idea behind flex layout is to give the container the ability to alter its items' width/height (and order) to best fill the available space (mostly to accommodate all kinds of display devices and screen sizes).

### Why Flexbox? <a name="why-flexbox"></a>
*   **Simpler Alignment**: Easily align items vertically and horizontally.
*   **Flexible Sizing**: Items can grow or shrink to fit available space.
*   **Order Control**: Change the visual order of items without changing the HTML source.
*   **Responsive Design**: Adapts well to different screen sizes.

### Main Axis and Cross Axis <a name="main-cross-axis"></a>
Understanding the main axis and cross axis is fundamental to Flexbox:
*   **Main Axis**: The primary axis along which flex items are laid out. It is defined by the `flex-direction` property.
    *   If `flex-direction: row` or `row-reverse`, the main axis is horizontal.
    *   If `flex-direction: column` or `column-reverse`, the main axis is vertical.
*   **Cross Axis**: The axis perpendicular to the main axis.
    *   If the main axis is horizontal, the cross axis is vertical.
    *   If the main axis is vertical, the cross axis is horizontal.

![Main and Cross Axis](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Basic_Concepts_of_Flexbox/flex_terms.png) *(Image credit: MDN Web Docs)*

## 2. Flex Container Properties <a name="flex-container-properties"></a>

These properties are applied to the parent element (the flex container).

### `display: flex` or `display: inline-flex` <a name="display-flex"></a>
*   Turns an element into a flex container.
*   `display: flex;`: The container behaves like a block-level element.
*   `display: inline-flex;`: The container behaves like an inline-level element.
    ```css
    .container {
      display: flex; /* or inline-flex */
    }
    ```

### `flex-direction` <a name="flex-direction"></a>
*   Defines the direction of the main axis (how flex items are placed in the flex container).
*   Values:
    *   `row` (default): Items are laid out horizontally, left to right.
    *   `row-reverse`: Items are laid out horizontally, right to left.
    *   `column`: Items are laid out vertically, top to bottom.
    *   `column-reverse`: Items are laid out vertically, bottom to top.
    ```css
    .container {
      display: flex;
      flex-direction: row; /* Default */
    }
    ```

### `flex-wrap` <a name="flex-wrap"></a>
*   Controls whether flex items are forced onto one line or can wrap onto multiple lines if they don't fit.
*   Values:
    *   `nowrap` (default): All flex items will be on one line (may cause overflow).
    *   `wrap`: Flex items will wrap onto multiple lines if needed, from top to bottom.
    *   `wrap-reverse`: Flex items will wrap onto multiple lines if needed, from bottom to top.
    ```css
    .container {
      display: flex;
      flex-wrap: wrap;
    }
    ```

### `flex-flow` (shorthand for `flex-direction` and `flex-wrap`) <a name="flex-flow"></a>
*   A shorthand property for setting both `flex-direction` and `flex-wrap`.
    ```css
    .container {
      display: flex;
      flex-flow: column wrap; /* Main axis is vertical, items can wrap */
    }
    ```

### `justify-content` <a name="justify-content"></a>
*   Aligns flex items along the **main axis** of the current line.
*   Values:
    *   `flex-start` (default): Items are packed toward the start of the main axis.
    *   `flex-end`: Items are packed toward the end of the main axis.
    *   `center`: Items are centered along the main axis.
    *   `space-between`: Items are evenly distributed; first item at the start, last item at the end, space is between items.
    *   `space-around`: Items are evenly distributed with equal space around them (half-size space on the ends).
    *   `space-evenly`: Items are distributed so that the spacing between any two items (and the space to the edges) is equal.
    ```css
    .container {
      display: flex;
      justify-content: center;
    }
    ```

### `align-items` <a name="align-items"></a>
*   Aligns flex items along the **cross axis** of the current line.
*   Values:
    *   `stretch` (default): Flex items are stretched to fill the container's height (if `flex-direction: row`) or width (if `flex-direction: column`), respecting `min-width`/`max-width` or `min-height`/`max-height`.
    *   `flex-start`: Items are aligned to the start of the cross axis.
    *   `flex-end`: Items are aligned to the end of the cross axis.
    *   `center`: Items are centered along the cross axis.
    *   `baseline`: Items are aligned such that their baselines align.
    ```css
    .container {
      display: flex;
      height: 200px; /* Container needs a defined height for align-items to be visible */
      align-items: center;
    }
    ```

### `align-content` <a name="align-content"></a>
*   Aligns the **lines** of flex items within the flex container when there is extra space in the **cross axis** (i.e., when `flex-wrap: wrap` is used and there are multiple lines).
*   Has no effect when there is only one line of flex items.
*   Values (similar to `justify-content` but for the cross axis and lines):
    *   `stretch` (default): Lines stretch to take up the remaining space.
    *   `flex-start`: Lines packed to the start of the container.
    *   `flex-end`: Lines packed to the end of the container.
    *   `center`: Lines packed to the center of the container.
    *   `space-between`: Lines evenly distributed; first line at the start, last at the end.
    *   `space-around`: Lines evenly distributed with equal space around each line.
    *   `space-evenly`: Lines are evenly distributed with equal space around them.
    ```css
    .container {
      display: flex;
      flex-wrap: wrap;
      height: 300px; /* Must have a defined height and multiple lines */
      align-content: space-around;
    }
    ```

### `gap`, `row-gap`, `column-gap` <a name="gap"></a>
*   Specifies the gap between flex items (gutters).
*   `gap`: Shorthand for `row-gap` and `column-gap`.
*   `row-gap`: Specifies the gap between rows (when `flex-wrap: wrap` is used).
*   `column-gap`: Specifies the gap between columns.
    ```css
    .container {
      display: flex;
      gap: 10px; /* 10px gap for both rows and columns */
      /* or */
      row-gap: 20px;
      column-gap: 5px;
    }
    ```

## 3. Flex Item Properties <a name="flex-item-properties"></a>

These properties are applied to the children of the flex container (the flex items).

### `order` <a name="order"></a>
*   Controls the order in which flex items appear in the flex container.
*   Default value is `0`.
*   Items are laid out in ascending order of the `order` value. Items with the same `order` value are laid out in the source order.
    ```css
    .item-1 { order: 2; }
    .item-2 { order: 1; } /* Will appear before item-1 */
    .item-3 { order: 3; }
    ```

### `flex-grow` <a name="flex-grow"></a>
*   Defines the ability for a flex item to grow if necessary. It accepts a unitless value that serves as a proportion.
*   Default value is `0` (item does not grow).
*   If all items have `flex-grow: 1`, they will receive an equal share of the remaining space. If one item has `flex-grow: 2` and others `flex-grow: 1`, the one with `2` will take twice as much space as the others.
    ```css
    .item {
      flex-grow: 1; /* Item can grow to fill available space */
    }
    ```

### `flex-shrink` <a name="flex-shrink"></a>
*   Defines the ability for a flex item to shrink if necessary.
*   Default value is `1` (item can shrink).
*   A value of `0` means the item will not shrink, even if it causes overflow.
*   Higher values mean the item will shrink more relative to other items.
    ```css
    .item {
      flex-shrink: 0; /* Item will not shrink */
    }
    ```

### `flex-basis` <a name="flex-basis"></a>
*   Defines the default size of an element before the remaining space is distributed. It can be a length (e.g., `20%`, `5rem`, etc.) or the keyword `auto`.
*   If set to `auto`, it looks at its `width` or `height` property (or content size if not set).
*   If set to a specific value, that value is used as the initial size along the main axis.
    ```css
    .item {
      flex-basis: 200px; /* Initial size before growing/shrinking */
    }
    ```

### `flex` (shorthand for `flex-grow`, `flex-shrink`, and `flex-basis`) <a name="flex-shorthand"></a>
*   The most common way to set flex item properties.
*   Syntax: `flex: <flex-grow> <flex-shrink> <flex-basis>`
*   Defaults: `flex: 0 1 auto`
*   Common values:
    *   `flex: auto` (equivalent to `1 1 auto`)
    *   `flex: none` (equivalent to `0 0 auto` - item is not flexible)
    *   `flex: 1` (equivalent to `1 1 0%` - item takes an equal share of space, often used for equal-width columns)
    *   `flex: 0 0 200px` (item is fixed at 200px, won't grow or shrink)
    ```css
    .item {
      flex: 1 1 100px; /* Grow:1, Shrink:1, Basis:100px */
    }
    ```

### `align-self` <a name="align-self"></a>
*   Allows the default alignment (or the one specified by `align-items` on the container) to be overridden for individual flex items.
*   Values are the same as `align-items`: `auto` (default), `flex-start`, `flex-end`, `center`, `baseline`, `stretch`.
    ```css
    .container {
      display: flex;
      align-items: flex-start;
    }
    .special-item {
      align-self: center; /* This item will be centered, others remain at flex-start */
    }
    ```

## 4. Common Use Cases <a name="common-use-cases"></a>

*   **Navigation Bars**: Easily create horizontal or vertical navigation menus where items space out correctly.
*   **Card Layouts**: Arrange cards in a row that wrap and have equal height.
*   **Centering Elements**: `display: flex; justify-content: center; align-items: center;` on a parent will perfectly center a child element.
*   **Distributing Space**: Use `justify-content` and `flex-grow` to manage how space is allocated among items.
*   **Holy Grail Layout**: While Grid is often better, Flexbox can be used for parts of this classic layout.

## 5. Flexbox vs. CSS Grid <a name="flexbox-vs-grid"></a>

*   **Flexbox** is primarily for **one-dimensional layouts** (either a row or a column).
*   **CSS Grid** is for **two-dimensional layouts** (rows AND columns simultaneously).
*   They can be used together; it's common to use Grid for the overall page layout and Flexbox for components within grid areas.

## 6. Browser Support and Prefixes <a name="browser-support"></a>

Flexbox is widely supported by all modern browsers. For older browsers (like IE 10 and some older mobile browsers), you might need vendor prefixes (`-webkit-`, `-ms-`). However, for most projects today, standard unprefixed Flexbox is sufficient.

## 7. Key Takeaways <a name="key-takeaways"></a>

*   Flexbox is a powerful one-dimensional layout system.
*   Understand the **main axis** and **cross axis** as they are fundamental.
*   **Flex container properties** (`display`, `flex-direction`, `flex-wrap`, `justify-content`, `align-items`, `align-content`, `gap`) control the overall layout of items.
*   **Flex item properties** (`order`, `flex-grow`, `flex-shrink`, `flex-basis`, `flex` shorthand, `align-self`) control individual items within the container.
*   Flexbox simplifies many common layout tasks like alignment, spacing, and ordering.

Mastering Flexbox is essential for modern web development and creating responsive, adaptable user interfaces.

---

⬅️ [Back to CSS Specificity](../09-css-specificity/README.md) | ➡️ [Next Section: CSS Grid](../11-css-grid/README.md)
