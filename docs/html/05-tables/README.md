# 5. HTML Tables

HTML tables are used to arrange data—like text, images, links, other tables, etc.—into rows and columns. While CSS is now primarily used for page layout, tables still have a crucial role in displaying tabular data.

## Table of Contents
1. [Basic Table Structure](#basic-table-structure)
   - [`<table>` Element](#table-element)
   - [`<tr>` (Table Row) Element](#tr-element)
   - [`<td>` (Table Data Cell) Element](#td-element)
   - [`<th>` (Table Header Cell) Element](#th-element)
2. [Table Caption (`<caption>`)](#table-caption)
3. [Spanning Rows and Columns](#spanning-rows-and-columns)
   - [`colspan` Attribute](#colspan-attribute)
   - [`rowspan` Attribute](#rowspan-attribute)
4. [Grouping Table Content](#grouping-table-content)
   - [`<thead>` (Table Head)](#thead-element)
   - [`<tbody>` (Table Body)](#tbody-element)
   - [`<tfoot>` (Table Foot)](#tfoot-element)
   - [Benefits of Grouping](#benefits-of-grouping)
5. [Styling Tables (Brief Overview)](#styling-tables)
6. [Accessibility for Tables](#accessibility-for-tables)
   - [Using `<th>` Correctly](#using-th-correctly)
   - [The `scope` Attribute](#scope-attribute)
7. [When to Use Tables (and When Not To)](#when-to-use-tables)
8. [Key Takeaways](#key-takeaways)

## Basic Table Structure <a name="basic-table-structure"></a>

A simple HTML table is constructed using a few core elements:

### `<table>` Element <a name="table-element"></a>
*   The `<table>` element is the container for all table content.

### `<tr>` (Table Row) Element <a name="tr-element"></a>
*   The `<tr>` element defines a row in the table.

### `<td>` (Table Data Cell) Element <a name="td-element"></a>
*   The `<td>` element defines a standard data cell within a table row. This is where the actual data content goes.

### `<th>` (Table Header Cell) Element <a name="th-element"></a>
*   The `<th>` element defines a header cell in a table. 
*   Text within `<th>` elements is typically rendered as bold and centered by browsers.
*   Header cells provide semantic meaning, indicating that they are headers for columns or rows, which is important for accessibility.

**Basic Example:**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Basic Table Example</title>
    <style>
        /* Basic styling for readability */
        table, th, td {
            border: 1px solid black;
            border-collapse: collapse; /* Merges borders between cells */
        }
        th, td {
            padding: 8px;
            text-align: left;
        }
    </style>
</head>
<body>

    <h2>Student Grades</h2>
    <table>
        <tr>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Grade</th>
        </tr>
        <tr>
            <td>Alice</td>
            <td>Smith</td>
            <td>A</td>
        </tr>
        <tr>
            <td>Bob</td>
            <td>Johnson</td>
            <td>B+</td>
        </tr>
        <tr>
            <td>Charlie</td>
            <td>Brown</td>
            <td>C</td>
        </tr>
    </table>

</body>
</html>
```

In this example:
*   The first `<tr>` contains `<th>` elements, defining the header row.
*   Subsequent `<tr>` elements contain `<td>` elements, defining the data rows.

## Table Caption (`<caption>`) <a name="table-caption"></a>

*   The `<caption>` element provides a title or explanation for the table.
*   It should be the **first child** of the `<table>` element.
*   A caption helps users understand the context of the table data, especially for accessibility.

**Example with Caption:**
```html
<table>
    <caption>Monthly Sales Report</caption>
    <tr>
        <th>Month</th>
        <th>Sales</th>
    </tr>
    <tr>
        <td>January</td>
        <td>$10,000</td>
    </tr>
    <tr>
        <td>February</td>
        <td>$12,500</td>
    </tr>
</table>
```

## Spanning Rows and Columns <a name="spanning-rows-and-columns"></a>

You can make a cell span multiple columns or rows using the `colspan` and `rowspan` attributes.

### `colspan` Attribute <a name="colspan-attribute"></a>
*   The `colspan` attribute on a `<th>` or `<td>` specifies how many columns that cell should span (extend across).

**Example with `colspan`:**
```html
<table>
    <caption>Contact Information</caption>
    <tr>
        <th>Name</th>
        <th colspan="2">Contact Details</th> <!-- This header spans 2 columns -->
    </tr>
    <tr>
        <td>John Doe</td>
        <td>john.doe@email.com</td>
        <td>(555) 123-4567</td>
    </tr>
    <tr>
        <td>Jane Roe</td>
        <td>jane.roe@email.com</td>
        <td>(555) 987-6543</td>
    </tr>
</table>
```

### `rowspan` Attribute <a name="rowspan-attribute"></a>
*   The `rowspan` attribute on a `<th>` or `<td>` specifies how many rows that cell should span (extend down).

**Example with `rowspan`:**
```html
<table>
    <caption>Schedule</caption>
    <tr>
        <th>Time</th>
        <th>Activity</th>
    </tr>
    <tr>
        <td>9:00 AM</td>
        <td>Meeting</td>
    </tr>
    <tr>
        <td rowspan="2">10:00 AM</td> <!-- This cell spans 2 rows -->
        <td>Project Work</td>
    </tr>
    <tr>
        <!-- No cell needed here for the first column as it's spanned -->
        <td>Follow-up Calls</td>
    </tr>
    <tr>
        <td>12:00 PM</td>
        <td>Lunch</td>
    </tr>
</table>
```

## Grouping Table Content <a name="grouping-table-content"></a>

For more complex tables, HTML provides elements to group header, body, and footer content: `<thead>`, `<tbody>`, and `<tfoot>`.

### `<thead>` (Table Head) <a name="thead-element"></a>
*   The `<thead>` element is used to group header content in an HTML table.
*   It should appear after any `<caption>` and before any `<tbody>` or `<tfoot>` elements.
*   It can contain one or more `<tr>` elements with `<th>` cells.

### `<tbody>` (Table Body) <a name="tbody-element"></a>
*   The `<tbody>` element is used to group the main body content in an HTML table.
*   It should appear after any `<caption>` and `<thead>`, and before any `<tfoot>` (though modern HTML allows `<tfoot>` to appear before or after `<tbody>`).
*   It contains the primary data rows (`<tr>` with `<td>` cells).
*   A table can have multiple `<tbody>` elements to group different sections of data.

### `<tfoot>` (Table Foot) <a name="tfoot-element"></a>
*   The `<tfoot>` element is used to group footer content in an HTML table.
*   It typically contains summary information for the columns, like totals.
*   It should appear after `<caption>` and `<thead>`.

**Example with Grouping Elements:**
```html
<table>
    <caption>Product Inventory</caption>
    <thead>
        <tr>
            <th>Product ID</th>
            <th>Product Name</th>
            <th>Quantity</th>
            <th>Price</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>P101</td>
            <td>Widget A</td>
            <td>150</td>
            <td>$10.00</td>
        </tr>
        <tr>
            <td>P102</td>
            <td>Gadget B</td>
            <td>75</td>
            <td>$25.50</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td colspan="3">Total Items:</td>
            <td>225</td>
        </tr>
    </tfoot>
</table>
```

### Benefits of Grouping <a name="benefits-of-grouping"></a>
*   **Semantic Structure**: Clearly defines the different parts of the table.
*   **Styling**: Allows for separate styling of header, body, and footer sections using CSS.
*   **Functionality**: When printing long tables that span multiple pages, browsers can repeat the `<thead>` and `<tfoot>` content on each page.

## Styling Tables (Brief Overview) <a name="styling-tables"></a>

While HTML defines the structure of tables, their appearance is controlled by CSS. Common CSS properties for tables include:
*   `border`: To add borders to the table and cells.
*   `border-collapse`: To control whether borders are separated or collapsed.
*   `padding`: To add space inside cells.
*   `text-align`: To align text within cells.
*   `background-color`: To set background colors for rows, columns, or cells (e.g., for zebra-striping rows).
*   `width`, `height`: To control table and cell dimensions.

We'll cover CSS in detail in a later module.

## Accessibility for Tables <a name="accessibility-for-tables"></a>

Making tables accessible is crucial for users who rely on assistive technologies like screen readers.

### Using `<th>` Correctly <a name="using-th-correctly"></a>
*   Always use `<th>` for header cells. This programmatically associates header information with data cells.

### The `scope` Attribute <a name="scope-attribute"></a>
*   For more complex tables, the `scope` attribute on `<th>` cells explicitly defines whether the header applies to a column or a row.
    *   `scope="col"`: The header applies to all cells in that column.
    *   `scope="row"`: The header applies to all cells in that row.

**Example with `scope`:**
```html
<table>
    <caption>Quarterly Sales by Region</caption>
    <thead>
        <tr>
            <th scope="col">Region</th>
            <th scope="col">Q1 Sales</th>
            <th scope="col">Q2 Sales</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th scope="row">North</th> <!-- Row header -->
            <td>$50,000</td>
            <td>$55,000</td>
        </tr>
        <tr>
            <th scope="row">South</th> <!-- Row header -->
            <td>$45,000</td>
            <td>$48,000</td>
        </tr>
    </tbody>
</table>
```

## When to Use Tables (and When Not To) <a name="when-to-use-tables"></a>

*   **Do Use Tables For**: Displaying actual tabular data – information that naturally fits into a grid of rows and columns (e.g., spreadsheets, statistics, product comparisons).
*   **Do Not Use Tables For**: Page layout. In the past, tables were often used to structure the layout of entire web pages (e.g., creating columns for sidebars and main content). This is now considered bad practice. CSS (specifically Flexbox and Grid) should be used for page layout as it's more flexible, semantic, and accessible.

## Key Takeaways

*   Tables are created with `<table>`, `<tr>` (rows), `<th>` (headers), and `<td>` (data cells).
*   `<caption>` provides a title for the table.
*   `colspan` and `rowspan` allow cells to span multiple columns or rows.
*   `<thead>`, `<tbody>`, and `<tfoot>` group table content semantically.
*   Use `<th>` and the `scope` attribute for table accessibility.
*   Tables are for **tabular data**, not for page layout.

Understanding how to structure tables correctly is essential for presenting data clearly and accessibly on the web.

---

🔙 [Back to Links and Images](../04-links-and-images/README.md) | ➡️ [Next Section: Forms](../06-forms/README.md)
