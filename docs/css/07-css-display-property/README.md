# 7. The CSS `display` Property

The `display` property in CSS is one of the most crucial properties for controlling layout. It specifies how an element should be shown on the page and how it interacts with other elements in terms of document flow, sizing, and positioning.

## Table of Contents
1.  [Introduction to `display`](#intro-display)
2.  [Common `display` Values](#common-values)
    *   [`block`](#display-block)
    *   [`inline`](#display-inline)
    *   [`inline-block`](#display-inline-block)
    *   [`none`](#display-none)
3.  [Layout-Specific `display` Values](#layout-specific-values)
    *   [`flex`](#display-flex)
    *   [`inline-flex`](#display-inline-flex)
    *   [`grid`](#display-grid)
    *   [`inline-grid`](#display-inline-grid)
4.  [Table-Related `display` Values](#table-values)
    *   [`table`](#display-table)
    *   [`table-row`, `table-cell`, etc.](#table-parts)
5.  [Other `display` Values](#other-values)
    *   [`list-item`](#display-list-item)
    *   [`contents`](#display-contents)
    *   [`flow-root`](#display-flow-root)
6.  [Understanding Outer and Inner Display Types](#outer-inner-display)
7.  [Choosing the Right `display` Value](#choosing-display)
8.  [Key Takeaways](#key-takeaways)

## 1. Introduction to `display` <a name="intro-display"></a>

Every HTML element has a default `display` value (usually `block` or `inline` depending on the element type). The `display` property allows you to override this default behavior. Understanding how different `display` values work is key to mastering CSS layout.

## 2. Common `display` Values <a name="common-values"></a>

These are the most frequently used `display` values that form the basis of many layouts.

### `block` <a name="display-block"></a>
*   **Characteristics**:
    *   Starts on a new line and takes up the full width available (stretches out to the left and right as far as it can within its container).
    *   `width`, `height`, `margin` (top/bottom/left/right), and `padding` properties are respected.
    *   If `width` is not specified, it defaults to `auto` (full width of its container).
    *   If `height` is not specified, it defaults to `auto` (height of its content).
*   **Common Block-Level Elements**: `<div>`, `<p>`, `<h1>`-`<h6>`, `<ul>`, `<ol>`, `<li>`, `<form>`, `<header>`, `<footer>`, `<section>`, `<article>`.
    ```css
    div {
      display: block;
      width: 200px;
      height: 100px;
      background-color: lightblue;
      margin: 10px;
      padding: 15px;
    }
    ```
    ```html
    <div>This is a block element.</div>
    <div>This is another block element, starting on a new line.</div>
    ```

### `inline` <a name="display-inline"></a>
*   **Characteristics**:
    *   Does **not** start on a new line; it flows along with surrounding text and other inline elements.
    *   Takes up only as much width as necessary for its content.
    *   `width` and `height` properties are **not** respected.
    *   `margin-top` and `margin-bottom` are **not** respected. `margin-left` and `margin-right` are respected.
    *   `padding-top` and `padding-bottom` are respected visually, but they do not affect the layout of surrounding elements (they might overlap). `padding-left` and `padding-right` are respected and affect layout.
*   **Common Inline Elements**: `<span>`, `<a>`, `<img>`, `<strong>`, `<em>`, `<input>` (some types), `<button>` (can behave like inline-block in some contexts).
    ```css
    span {
      display: inline;
      background-color: lightcoral;
      margin: 10px; /* Top/bottom margin won't apply effectively */
      padding: 5px;
      /* width: 100px; height: 50px; -- These would be ignored */
    }
    ```
    ```html
    <p>This is a paragraph with an <span>inline span</span> element inside it. The span flows with the text.</p>
    ```

### `inline-block` <a name="display-inline-block"></a>
*   **Characteristics**:
    *   Combines features of `block` and `inline`.
    *   Flows with surrounding content like an `inline` element (does not start on a new line).
    *   However, `width`, `height`, `margin` (all sides), and `padding` (all sides) are respected, just like a `block` element.
*   Useful for creating items in a row that need specific dimensions, like navigation links or small cards.
    ```css
    .inline-block-item {
      display: inline-block;
      width: 150px;
      height: 80px;
      background-color: lightgreen;
      margin: 10px;
      padding: 10px;
      text-align: center;
      line-height: 60px; /* For vertical centering if height is fixed */
    }
    ```
    ```html
    <div class="inline-block-item">Item 1</div>
    <div class="inline-block-item">Item 2</div>
    <div class="inline-block-item">Item 3</div>
    ```
    *Note: `inline-block` elements are affected by `white-space` in HTML, which can cause small gaps between them. This can be managed by various techniques (e.g., setting `font-size: 0` on the parent and resetting it on children, or removing white space in HTML).*

### `none` <a name="display-none"></a>
*   **Characteristics**:
    *   The element is completely removed from the document flow.
    *   It takes up no space, and it's as if it doesn't exist on the page.
    *   Descendant elements are also not displayed.
    *   This is different from `visibility: hidden;`, which hides the element but still reserves its space in the layout.
*   Often used with JavaScript to show/hide elements dynamically.
    ```css
    .hidden-element {
      display: none;
    }
    ```
    ```html
    <div>This element is visible.</div>
    <div class="hidden-element">This element is completely hidden and takes no space.</div>
    <div>This element appears right after the first visible one.</div>
    ```

## 3. Layout-Specific `display` Values <a name="layout-specific-values"></a>

These values turn an element into a container for a specific layout model, affecting its direct children.

### `flex` <a name="display-flex"></a>
*   **Characteristics**:
    *   Turns an element into a **flex container**, and its direct children become **flex items**.
    *   Enables the Flexbox layout model, designed for creating complex, one-dimensional layouts (rows or columns) with powerful alignment and sizing capabilities.
    *   The flex container itself behaves like a `block`-level element.
*   Flexbox will be covered in detail in a dedicated module.
    ```css
    .flex-container {
      display: flex;
      background-color: #eee;
      padding: 10px;
    }
    .flex-container > div {
      background-color: dodgerblue;
      color: white;
      margin: 5px;
      padding: 10px;
      flex: 1; /* Example: children share space equally */
    }
    ```
    ```html
    <div class="flex-container">
      <div>Item 1</div>
      <div>Item 2</div>
      <div>Item 3</div>
    </div>
    ```

### `inline-flex` <a name="display-inline-flex"></a>
*   **Characteristics**:
    *   Similar to `flex`, it turns an element into a flex container, and its children become flex items.
    *   However, the flex container itself behaves like an `inline-block` element (flows with surrounding content, respects width/height if set).
    ```css
    .inline-flex-container {
      display: inline-flex;
      border: 1px solid green;
    }
    /* Children styling same as .flex-container > div */
    ```
    ```html
    Some text before <div class="inline-flex-container"><div>A</div><div>B</div></div> some text after.
    ```

### `grid` <a name="display-grid"></a>
*   **Characteristics**:
    *   Turns an element into a **grid container**, and its direct children become **grid items**.
    *   Enables the CSS Grid Layout model, designed for creating complex, two-dimensional layouts (rows AND columns).
    *   The grid container itself behaves like a `block`-level element.
*   CSS Grid will be covered in detail in a dedicated module.
    ```css
    .grid-container {
      display: grid;
      grid-template-columns: auto auto auto; /* Example: 3 columns */
      background-color: #eee;
      padding: 10px;
      gap: 10px;
    }
    .grid-container > div {
      background-color: tomato;
      color: white;
      padding: 20px;
      text-align: center;
    }
    ```
    ```html
    <div class="grid-container">
      <div>1</div>
      <div>2</div>
      <div>3</div>
      <div>4</div>
      <div>5</div>
      <div>6</div>
    </div>
    ```

### `inline-grid` <a name="display-inline-grid"></a>
*   **Characteristics**:
    *   Similar to `grid`, it turns an element into a grid container, and its children become grid items.
    *   However, the grid container itself behaves like an `inline-block` element.

## 4. Table-Related `display` Values <a name="table-values"></a>

These values allow non-table elements to behave like HTML table elements. While CSS Grid and Flexbox are generally preferred for page layout, these can be useful in specific scenarios or for styling actual table data when semantic HTML tables are not used.

*   `display: table;` (behaves like `<table>`)
*   `display: table-row;` (behaves like `<tr>`)
*   `display: table-cell;` (behaves like `<td>` or `<th>`)
*   `display: table-caption;` (behaves like `<caption>`)
*   `display: table-column-group;` (behaves like `<colgroup>`)
*   `display: table-header-group;` (behaves like `<thead>`)
*   `display: table-footer-group;` (behaves like `<tfoot>`)
*   `display: table-row-group;` (behaves like `<tbody>`)
*   `display: inline-table;` (table itself is inline-block, content is table layout)

    ```css
    .my-table {
      display: table;
      width: 100%;
      border-collapse: collapse;
    }
    .my-row {
      display: table-row;
    }
    .my-cell {
      display: table-cell;
      border: 1px solid #ccc;
      padding: 8px;
    }
    ```
    ```html
    <div class="my-table">
      <div class="my-row">
        <div class="my-cell">Cell 1.1</div>
        <div class="my-cell">Cell 1.2</div>
      </div>
      <div class="my-row">
        <div class="my-cell">Cell 2.1</div>
        <div class="my-cell">Cell 2.2</div>
      </div>
    </div>
    ```

## 5. Other `display` Values <a name="other-values"></a>

### `list-item` <a name="display-list-item"></a>
*   Makes an element behave like an `<li>` element. It generates a block box for the content and, depending on `list-style-type` and `list-style-position`, a marker box (bullet or number).
    ```css
    .custom-list-item {
      display: list-item;
      list-style-type: square;
      margin-left: 20px;
    }
    ```

### `contents` <a name="display-contents"></a>
*   **Characteristics**:
    *   The element itself does not generate any specific boxes. It's replaced by its children or pseudo-elements.
    *   Essentially, the element's children behave as if they were direct children of the element's parent.
    *   This can be useful for semantic grouping without adding an extra layer of boxes that might interfere with layouts like Flexbox or Grid, but it has accessibility implications (the element itself might be removed from the accessibility tree). Use with caution.
    ```html
    <div style="display: flex;">
      <div style="display: contents;">
        <!-- The div with display:contents is 'skipped' for layout -->
        <span>Flex Item 1 (from inner div)</span>
        <span>Flex Item 2 (from inner div)</span>
      </div>
      <span>Flex Item 3 (from outer div)</span>
    </div>
    ```

### `flow-root` <a name="display-flow-root"></a>
*   **Characteristics**:
    *   The element generates a block container box, and it always establishes a new block formatting context (BFC) for its content.
    *   A BFC is an area where floats are contained, and margins do not collapse across its boundaries.
    *   `display: flow-root;` is a modern way to create a BFC to contain floats without using older hacks like `overflow: hidden;` or clearfix methods.
    ```css
    .container-with-float {
      display: flow-root; /* Contains the float */
      border: 1px solid red;
    }
    .floated-element {
      float: left;
      width: 100px;
      height: 50px;
      background-color: lightblue;
    }
    ```
    ```html
    <div class="container-with-float">
      <div class="floated-element">I am floated.</div>
      Some text that should not wrap under the float if the container is too short.
    </div>
    ```

## 6. Understanding Outer and Inner Display Types <a name="outer-inner-display"></a>

Modern CSS specifications describe `display` in terms of two components:
*   **Outer display type**: Defines how the element's box participates in the flow layout with its siblings (e.g., `block` or `inline`).
*   **Inner display type**: Defines how the children of the element are laid out (e.g., `flow` (normal flow), `flex`, `grid`, `table`).

For example:
*   `display: block;` is effectively `display: block flow;`
*   `display: inline;` is effectively `display: inline flow;`
*   `display: flex;` is effectively `display: block flex;` (itself a block, children are flex items)
*   `display: inline-flex;` is effectively `display: inline flex;` (itself inline, children are flex items)
*   `display: grid;` is effectively `display: block grid;`
*   `display: inline-grid;` is effectively `display: inline grid;`

While you usually use the single keyword values, understanding this underlying concept can help clarify how complex `display` values work.

## 7. Choosing the Right `display` Value <a name="choosing-display"></a>

*   **For general flow and stacking**: `block`, `inline`, `inline-block`.
*   **To hide elements**: `none`.
*   **For one-dimensional layouts (rows or columns)**: `flex` or `inline-flex`.
*   **For two-dimensional layouts (grids of rows and columns)**: `grid` or `inline-grid`.
*   **For mimicking table structure**: `table`, `table-row`, `table-cell`, etc. (use sparingly for layout, prefer semantic tables for tabular data).
*   **To contain floats**: `flow-root`.
*   **Special cases**: `list-item`, `contents` (with caution).

## 8. Key Takeaways <a name="key-takeaways"></a>

*   The `display` property is fundamental for controlling element layout and behavior.
*   `block` elements take full width and start on new lines.
*   `inline` elements flow with text and don't respect width/height.
*   `inline-block` elements flow like inline but respect width/height/margins/padding.
*   `none` completely removes an element from the layout.
*   `flex` and `grid` enable powerful modern layout systems (Flexbox and CSS Grid).
*   Table-related values allow non-table elements to mimic table behavior.
*   `flow-root` is useful for creating new block formatting contexts, especially for containing floats.

Mastering the `display` property and its various values is a cornerstone of CSS proficiency.

---

⬅️ [Back to CSS Units and Values](../06-css-units-and-values/README.md) | ➡️ [Next Section: CSS Positioning](../08-css-positioning/README.md)
