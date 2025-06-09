# 11. CSS Grid Layout

CSS Grid Layout (or simply "Grid") is a two-dimensional layout system for CSS, designed to make it easy to create complex web page layouts. Unlike Flexbox, which is primarily for one-dimensional layouts (either a row or a column), Grid excels at dividing a page into major regions or defining the relationship in terms of size, position, and layer, between parts of a control built from HTML primitives.

## Table of Contents
1.  [Introduction to CSS Grid](#intro-grid)
    *   [Why CSS Grid?](#why-grid)
    *   [Grid Terminology](#grid-terminology)
        *   Grid Container & Grid Items
        *   Grid Lines (Column Lines & Row Lines)
        *   Grid Tracks (Columns & Rows)
        *   Grid Cell
        *   Grid Area
        *   Gutters (Gaps)
2.  [Grid Container Properties](#grid-container-properties)
    *   [`display: grid` or `display: inline-grid`](#display-grid)
    *   [`grid-template-columns` and `grid-template-rows`](#grid-template-columns-rows)
        *   Using Lengths, Percentages, `fr` unit, `auto`
        *   `repeat()` function
        *   `minmax()` function
    *   [`grid-template-areas`](#grid-template-areas)
    *   [`grid-auto-columns` and `grid-auto-rows`](#grid-auto-columns-rows)
    *   [`grid-auto-flow`](#grid-auto-flow)
    *   [`gap`, `row-gap`, `column-gap`](#gap-grid)
    *   [`justify-items`](#justify-items-container)
    *   [`align-items`](#align-items-container)
    *   [`place-items` (shorthand for `align-items` and `justify-items`)](#place-items-container)
    *   [`justify-content`](#justify-content-container) (for aligning the grid within the container)
    *   [`align-content`](#align-content-container) (for aligning the grid within the container)
    *   [`place-content` (shorthand for `align-content` and `justify-content`)](#place-content-container)
3.  [Grid Item Properties](#grid-item-properties)
    *   [`grid-column-start`, `grid-column-end`, `grid-row-start`, `grid-row-end`](#grid-column-row-start-end)
    *   [`grid-column` (shorthand) and `grid-row` (shorthand)](#grid-column-row-shorthand)
        *   Using line numbers
        *   Using `span`
        *   Using named grid lines
    *   [`grid-area`](#grid-area-item)
    *   [`justify-self`](#justify-self-item)
    *   [`align-self`](#align-self-item)
    *   [`place-self` (shorthand for `align-self` and `justify-self`)](#place-self-item)
    *   [`order`](#order-grid) (less common in Grid than Flexbox, but works)
    *   [`z-index`](#z-index-grid) (for overlapping items)
4.  [The `fr` Unit](#fr-unit)
5.  [The `repeat()` Notation](#repeat-notation)
6.  [The `minmax()` Function](#minmax-function)
7.  [Named Grid Lines and Areas](#named-lines-areas)
8.  [Implicit vs. Explicit Grid](#implicit-explicit-grid)
9.  [Common Use Cases](#common-use-cases-grid)
    *   Page Layouts (Headers, Footers, Sidebars, Main Content)
    *   Component Layouts (Galleries, Forms)
10. [CSS Grid vs. Flexbox](#grid-vs-flexbox-compare)
11. [Browser Support](#browser-support-grid)
12. [Key Takeaways](#key-takeaways-grid)

## 1. Introduction to CSS Grid <a name="intro-grid"></a>

Grid layout gives you a method of creating grid structures described in CSS and not in HTML. It helps you to create layouts that can be redefined using Media Queries, and adapt to different contexts.

### Why CSS Grid? <a name="why-grid"></a>
*   **Two-Dimensional Layouts**: Natively handles both rows and columns.
*   **Complex Page Structures**: Ideal for overall page layout, dividing the page into major sections.
*   **Reduced Markup**: Achieves complex layouts with simpler HTML.
*   **Alignment Control**: Powerful alignment capabilities for both the grid itself and its items.
*   **Responsive Design**: Easily adapt grid layouts to different screen sizes.

### Grid Terminology <a name="grid-terminology"></a>

![Grid Terminology](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/grid_layout_terms.png) *(Image credit: MDN Web Docs)*

*   **Grid Container**: The element on which `display: grid` or `display: inline-grid` is applied. It's the direct parent of all grid items.
*   **Grid Items**: The direct children of the grid container.
*   **Grid Lines**: The dividing lines that make up the structure of the grid. They can be horizontal (row grid lines) or vertical (column grid lines). Lines are numbered starting from 1.
*   **Grid Tracks**: The space between two adjacent grid lines. You have column tracks and row tracks.
*   **Grid Cell**: The space between two adjacent row and two adjacent column grid lines. It's the smallest unit of a grid.
*   **Grid Area**: The total space surrounded by four grid lines. A grid area can be composed of any number of grid cells.
*   **Gutters (Gaps)**: The spaces between grid tracks, defined by `gap`, `row-gap`, or `column-gap`.

## 2. Grid Container Properties <a name="grid-container-properties"></a>

These properties are applied to the grid container.

### `display: grid` or `display: inline-grid` <a name="display-grid"></a>
*   Turns an element into a grid container.
*   `display: grid;`: The container behaves like a block-level element.
*   `display: inline-grid;`: The container behaves like an inline-level element.
    ```css
    .container {
      display: grid;
    }
    ```

### `grid-template-columns` and `grid-template-rows` <a name="grid-template-columns-rows"></a>
*   Define the columns and rows of the grid with a space-separated list of values. The values represent the track size, and the space between them represents the grid line.
*   **Track Sizing Values**:
    *   `<length>`: e.g., `100px`, `10em`.
    *   `<percentage>`: Relative to the inline size of the grid container.
    *   `fr` unit: A fractional unit representing a fraction of the available space in the grid container.
    *   `auto`: The size is determined by the size of the content or the size of the grid item.
    *   `max-content`: The largest max-content contribution of the grid items occupying the grid track.
    *   `min-content`: The largest min-content contribution of the grid items occupying the grid track.
*   **`repeat()` function**: For repeating track patterns (e.g., `repeat(3, 1fr)` for three equal columns).
*   **`minmax(min, max)` function**: Defines a size range, greater than or equal to min and less than or equal to max.
    ```css
    .container {
      display: grid;
      grid-template-columns: 100px 1fr 2fr; /* 3 columns: 100px, 1 part, 2 parts of remaining space */
      grid-template-rows: auto 150px repeat(2, 100px); /* 4 rows */
    }
    ```

### `grid-template-areas` <a name="grid-template-areas"></a>
*   Defines a grid template by referencing the names of the grid areas which are specified with the `grid-area` property on grid items.
*   Repeating the name of a grid area causes the content to span those cells. A period (`.`) signifies an empty cell.
    ```css
    .container {
      display: grid;
      grid-template-columns: 1fr 1fr 1fr;
      grid-template-rows: auto auto auto;
      grid-template-areas:
        "header header header"
        "sidebar main main"
        "footer footer footer";
    }
    .item-header { grid-area: header; }
    .item-sidebar { grid-area: sidebar; }
    .item-main { grid-area: main; }
    .item-footer { grid-area: footer; }
    ```

### `grid-auto-columns` and `grid-auto-rows` <a name="grid-auto-columns-rows"></a>
*   Specifies the size of any auto-generated grid tracks (also known as implicit grid tracks).
*   Implicit tracks are created when there are more grid items than cells defined in the explicit grid (e.g., by `grid-template-columns`/`rows`).
    ```css
    .container {
      display: grid;
      grid-template-columns: 100px 100px;
      grid-auto-rows: 50px; /* Implicitly created rows will be 50px tall */
    }
    ```

### `grid-auto-flow` <a name="grid-auto-flow"></a>
*   Controls how auto-placed items get flowed into the grid.
*   Values:
    *   `row` (default): Fills each row in turn, adding new rows as necessary.
    *   `column`: Fills each column in turn, adding new columns as necessary.
    *   `dense`: Attempts to fill in holes earlier in the grid if smaller items come up later. This may cause items to appear out of order.
    ```css
    .container {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      grid-auto-flow: row dense;
    }
    ```

### `gap`, `row-gap`, `column-gap` <a name="gap-grid"></a>
*   Specifies the size of the grid lines (gutters). Think of it like `padding` between cells.
*   `gap`: Shorthand for `row-gap` and `column-gap`.
*   `row-gap`: Specifies the gap between rows.
*   `column-gap`: Specifies the gap between columns.
    ```css
    .container {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px 10px; /* 20px row gap, 10px column gap */
      /* or */
      row-gap: 20px;
      column-gap: 10px;
    }
    ```

### `justify-items` <a name="justify-items-container"></a>
*   Aligns grid items along the **inline (row) axis** within their grid cell.
*   Values: `start`, `end`, `center`, `stretch` (default).
    ```css
    .container {
      display: grid;
      justify-items: center; /* All items centered horizontally in their cells */
    }
    ```

### `align-items` <a name="align-items-container"></a>
*   Aligns grid items along the **block (column) axis** within their grid cell.
*   Values: `start`, `end`, `center`, `stretch` (default), `baseline`.
    ```css
    .container {
      display: grid;
      align-items: center; /* All items centered vertically in their cells */
    }
    ```

### `place-items` (shorthand for `align-items` and `justify-items`) <a name="place-items-container"></a>
*   Shorthand for `align-items` and `justify-items`.
*   First value is `align-items`, second is `justify-items`. If second is omitted, first is used for both.
    ```css
    .container {
      display: grid;
      place-items: center start; /* align-items: center; justify-items: start; */
      /* place-items: center; -> align-items: center; justify-items: center; */
    }
    ```

### `justify-content` (for aligning the grid within the container) <a name="justify-content-container"></a>
*   Aligns the **entire grid** along the **inline (row) axis** when the total size of the grid is less than the grid container's size.
*   Values: `start`, `end`, `center`, `stretch`, `space-around`, `space-between`, `space-evenly`.
    ```css
    .container {
      display: grid;
      width: 500px;
      grid-template-columns: 100px 100px; /* Grid is 200px wide */
      justify-content: center; /* Centers the 200px grid within the 500px container */
    }
    ```

### `align-content` (for aligning the grid within the container) <a name="align-content-container"></a>
*   Aligns the **entire grid** along the **block (column) axis** when the total size of the grid is less than the grid container's size.
*   Values: `start`, `end`, `center`, `stretch`, `space-around`, `space-between`, `space-evenly`.
    ```css
    .container {
      display: grid;
      height: 500px;
      grid-template-rows: 100px 100px; /* Grid is 200px tall */
      align-content: space-between; /* Distributes space between the rows */
    }
    ```

### `place-content` (shorthand for `align-content` and `justify-content`) <a name="place-content-container"></a>
*   Shorthand for `align-content` and `justify-content`.
*   First value is `align-content`, second is `justify-content`. If second is omitted, first is used for both.
    ```css
    .container {
      display: grid;
      place-content: center space-between;
    }
    ```

## 3. Grid Item Properties <a name="grid-item-properties"></a>

These properties are applied to the grid items (direct children of the grid container).

### `grid-column-start`, `grid-column-end`, `grid-row-start`, `grid-row-end` <a name="grid-column-row-start-end"></a>
*   Determine a grid item's location within the grid by referring to specific grid lines.
*   `grid-column-start` / `grid-row-start`: The line where the item begins.
*   `grid-column-end` / `grid-row-end`: The line where the item ends.
    ```css
    .item-a {
      grid-column-start: 2;
      grid-column-end: 4; /* Spans from column line 2 to 4 (occupies 2 columns) */
      grid-row-start: 1;
      grid-row-end: 3;   /* Spans from row line 1 to 3 (occupies 2 rows) */
    }
    ```

### `grid-column` (shorthand) and `grid-row` (shorthand) <a name="grid-column-row-shorthand"></a>
*   Shorthand for `grid-column-start` / `grid-column-end` and `grid-row-start` / `grid-row-end` respectively.
*   Syntax: `<start-line> / <end-line>` or just `<start-line>` (spans 1 track by default).
*   Can use `span <number>` to specify how many tracks an item should span.
*   Can use named grid lines.
    ```css
    .item-b {
      grid-column: 1 / 3;       /* Starts at line 1, ends at line 3 */
      grid-row: 2 / span 2;   /* Starts at line 2, spans 2 rows */
    }
    .item-c {
      grid-column: col-start / col-end-wide; /* Using named lines */
    }
    ```

### `grid-area` <a name="grid-area-item"></a>
*   Gives an item a name so that it can be referenced by a template created with the `grid-template-areas` property.
*   Alternatively, can be a shorthand for `grid-row-start` / `grid-column-start` / `grid-row-end` / `grid-column-end`.
    ```css
    /* Using with grid-template-areas */
    .item-header { grid-area: header; }

    /* As a shorthand for line numbers */
    .item-d {
      grid-area: 2 / 1 / 4 / 3; /* row-start / col-start / row-end / col-end */
    }
    ```

### `justify-self` <a name="justify-self-item"></a>
*   Aligns a grid item inside its grid cell along the **inline (row) axis**.
*   Overrides `justify-items` on the container for this specific item.
*   Values: `start`, `end`, `center`, `stretch` (default).
    ```css
    .item-e {
      justify-self: end;
    }
    ```

### `align-self` <a name="align-self-item"></a>
*   Aligns a grid item inside its grid cell along the **block (column) axis**.
*   Overrides `align-items` on the container for this specific item.
*   Values: `start`, `end`, `center`, `stretch` (default), `baseline`.
    ```css
    .item-f {
      align-self: start;
    }
    ```

### `place-self` (shorthand for `align-self` and `justify-self`) <a name="place-self-item"></a>
*   Shorthand for `align-self` and `justify-self`.
*   First value is `align-self`, second is `justify-self`. If second is omitted, first is used for both.
    ```css
    .item-g {
      place-self: center end; /* align-self: center; justify-self: end; */
    }
    ```

### `order` <a name="order-grid"></a>
*   While Grid primarily uses placement properties, `order` can affect the painting order and tab order of items, especially for auto-placed items not explicitly positioned. It's less common for layout control in Grid compared to Flexbox.
*   Default is `0`.
    ```css
    .item-h { order: -1; } /* Might paint earlier or tab earlier */
    ```

### `z-index` <a name="z-index-grid"></a>
*   Controls the stacking order of grid items that overlap. An item with a higher `z-index` will be displayed in front of an item with a lower `z-index`.
*   Only works on positioned items (e.g., items whose `position` is not `static`) or grid items.
    ```css
    .item-i { z-index: 10; }
    ```

## 4. The `fr` Unit <a name="fr-unit"></a>
*   The `fr` unit represents a fraction of the available space in the grid container.
*   It's used to distribute space proportionally. For example, `1fr 2fr` means the second track will be twice as wide/tall as the first.
    ```css
    .container {
      display: grid;
      grid-template-columns: 1fr 2fr 1fr; /* Total 4fr; columns are 25%, 50%, 25% of available space */
    }
    ```

## 5. The `repeat()` Notation <a name="repeat-notation"></a>
*   A convenient way to define multiple tracks with the same or a repeating pattern.
*   Syntax: `repeat(<count>, <track-list>)` or `repeat(auto-fill | auto-fit, <track-list>)`.
    *   `auto-fill`: Fills the row with as many columns as it can fit. If the grid container has a definite size, then `auto-fill` allows spanning all columns.
    *   `auto-fit`: Behaves similarly to `auto-fill`, but after filling the grid, it collapses any empty tracks.
    ```css
    .container {
      display: grid;
      /* Creates 12 columns, each 1fr wide */
      grid-template-columns: repeat(12, 1fr);
      /* Creates as many 200px columns as can fit, with a 10px gap */
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 10px;
    }
    ```

## 6. The `minmax()` Function <a name="minmax-function"></a>
*   Defines a size range, greater than or equal to a minimum and less than or equal to a maximum.
*   Syntax: `minmax(<min>, <max>)`.
*   Very useful for responsive design, e.g., `minmax(200px, 1fr)` means the track will be at least `200px` but can grow to fill available space (`1fr`).
    ```css
    .container {
      display: grid;
      grid-template-columns: minmax(100px, 200px) 1fr minmax(150px, 30%);
    }
    ```

## 7. Named Grid Lines and Areas <a name="named-lines-areas"></a>
*   You can name grid lines explicitly when defining tracks, making your grid item placement more readable.
*   Named areas (using `grid-template-areas` and `grid-area` on items) provide a visual way to structure your layout.
    ```css
    .container {
      display: grid;
      grid-template-columns: [main-start] 1fr [content-start] 2fr [content-end] 1fr [main-end];
      grid-template-rows: [row1-start] auto [row1-end row2-start] auto [row2-end];
    }
    .item {
      grid-column: content-start / content-end;
      grid-row: row1-start;
    }
    ```

## 8. Implicit vs. Explicit Grid <a name="implicit-explicit-grid"></a>
*   **Explicit Grid**: The grid you define with `grid-template-columns`, `grid-template-rows`, and `grid-template-areas`.
*   **Implicit Grid**: If you place items outside the bounds of the explicit grid, or if you have more items than cells defined, the grid will create implicit tracks in the auto-placement algorithm. The size of these tracks is controlled by `grid-auto-columns` and `grid-auto-rows`.

## 9. Common Use Cases <a name="common-use-cases-grid"></a>
*   **Page Layouts**: Defining main regions like headers, footers, sidebars, main content areas.
*   **Component Layouts**: Complex components like image galleries, forms, dashboards.
*   **Overlapping Content**: Grid makes it easier to manage layers and overlapping elements with `z-index` and precise placement.

## 10. CSS Grid vs. Flexbox <a name="grid-vs-flexbox-compare"></a>
*   **Grid**: Two-dimensional (rows and columns). Designed for overall page layout. Content-out or layout-first.
*   **Flexbox**: One-dimensional (either a row or a column). Designed for components and smaller-scale layouts. Content-in or content-first.
*   **They are not mutually exclusive!** It's very common and powerful to use Grid for the main page structure and Flexbox for arranging items within a grid area.

## 11. Browser Support <a name="browser-support-grid"></a>
CSS Grid is widely supported by all modern browsers. For older browsers, support is non-existent or partial (e.g., IE10/11 had an older, prefixed version with different syntax). For most projects today, standard unprefixed Grid is sufficient.

## 12. Key Takeaways <a name="key-takeaways-grid"></a>
*   Grid is a powerful **two-dimensional** layout system.
*   Understand the core terminology: container, items, lines, tracks, cells, areas.
*   **Grid container properties** define the grid structure and alignment of the grid itself.
*   **Grid item properties** position and align individual items within the grid.
*   The `fr` unit, `repeat()`, and `minmax()` are essential for flexible and responsive grids.
*   Named lines and areas improve readability and maintainability.
*   Grid and Flexbox work well together to solve different layout challenges.

Mastering CSS Grid is crucial for creating sophisticated, responsive web layouts with clean and manageable CSS.

---

⬅️ [Back to CSS Flexbox](../10-css-flexbox/README.md) | ➡️ [Next Section: CSS Responsive Design](../12-css-responsive-design/README.md)
