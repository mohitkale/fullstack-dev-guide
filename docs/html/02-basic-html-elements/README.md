# 2. Basic HTML Elements

Now that you understand the basic structure of an HTML page, let's explore some of the most common HTML elements you'll use to build content. We'll also clarify the terms: tags, elements, and attributes.

## Table of Contents
1. [Understanding Tags, Elements, and Attributes](#understanding-tags-elements-and-attributes)
   - [Tags](#tags)
   - [Elements](#elements)
   - [Attributes](#attributes)
2. [Headings (`<h1>` to `<h6>`)](#headings)
3. [Paragraphs (`<p>`)](#paragraphs)
4. [Line Breaks (`<br>`)](#line-breaks)
5. [Horizontal Rules (`<hr>`)](#horizontal-rules)
6. [Comments in HTML (`<!-- ... -->`)](#comments-in-html)
7. [Nesting Elements](#nesting-elements)
8. [Empty Elements](#empty-elements)
9. [Key Takeaways](#key-takeaways)

## Understanding Tags, Elements, and Attributes <a name="understanding-tags-elements-and-attributes"></a>

These three terms are fundamental to HTML:

### Tags <a name="tags"></a>
*   Tags are the keywords surrounded by angle brackets (`<` and `>`).
*   They usually come in pairs: an **opening tag** (e.g., `<p>`) and a **closing tag** (e.g., `</p>`).
*   The closing tag is written like the opening tag, but with a forward slash (`/`) before the tag name.
*   Tags define the beginning and end of an HTML element.

### Elements <a name="elements"></a>
*   An HTML element consists of an opening tag, the content, and a closing tag.
*   **Example**: `<p>This is some text.</p>` is a paragraph element.
    *   `<p>` is the opening tag.
    *   `This is some text.` is the content.
    *   `</p>` is the closing tag.
*   Some HTML elements are **empty elements** (or void elements), meaning they don't have content or a closing tag. We'll see examples like `<br>` and `<img>` later.

### Attributes <a name="attributes"></a>
*   Attributes provide **additional information** about an HTML element.
*   They are always specified in the **opening tag**.
*   Attributes usually come in name/value pairs like: `name="value"`.
*   **Example**: `<a href="https://www.example.com">Visit Example</a>`
    *   `<a>` is the anchor element (for links).
    *   `href` is an attribute name.
    *   `"https://www.example.com"` is the attribute value.
    *   This attribute specifies the URL the link should go to.
*   Attribute values should generally be enclosed in double quotes (`""`). Single quotes (`''`) are also allowed, but double quotes are more common.

## Headings (`<h1>` to `<h6>`) <a name="headings"></a>

HTML provides six levels of headings, from `<h1>` (the most important) to `<h6>` (the least important).

*   Headings are used to define the hierarchical structure of your content.
*   `<h1>` should typically be used for the main title of a page or section, and you should generally have only one `<h1>` per page for SEO and accessibility reasons.
*   Use headings in a logical order (e.g., an `<h3>` should follow an `<h2>`, not jump directly from `<h1>` to `<h3>`).
*   Search engines use headings to index the structure and content of your web pages.

**Example:**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Headings Example</title>
</head>
<body>
    <h1>Main Title of the Page</h1>
    <p>This is a paragraph introducing the main topic.</p>
    <h2>A Major Section</h2>
    <p>Content related to the major section.</p>
    <h3>A Subsection</h3>
    <p>More detailed content within the subsection.</p>
    <h4>A Minor Heading</h4>
    <p>Even more specific details.</p>
    <h5>A Very Minor Heading</h5>
    <p>Fine-grained information.</p>
    <h6>The Smallest Heading</h6>
    <p>The least important heading level.</p>
</body>
</html>
```

**Best Practice**: Use headings for their semantic meaning (to structure content), not just to make text big or bold. Styling should be handled by CSS.

## Paragraphs (`<p>`) <a name="paragraphs"></a>

The `<p>` element defines a paragraph of text.

*   Browsers automatically add some white space (a margin) before and after each `<p>` element.
*   It's one of the most common block-level elements (meaning it typically starts on a new line and takes up the full width available).

**Example:**
```html
<p>This is the first paragraph. It contains some text that will be displayed as a block.</p>
<p>This is the second paragraph. Notice how it starts on a new line and has space separating it from the first paragraph.</p>
```

## Line Breaks (`<br>`) <a name="line-breaks"></a>

The `<br>` element inserts a single line break.

*   It's an **empty element**, meaning it has no content and no closing tag. You can write it as `<br>` or `<br />` (the latter is XHTML-style, but both work in HTML5).
*   Use `<br>` when you want a line break that is part of the content itself, like in a poem or an address.
*   **Avoid using `<br>` to create vertical space between paragraphs or other block elements.** Use CSS margins and padding for that purpose.

**Example:**
```html
<p>Roses are red,<br>
Violets are blue,<br>
HTML is fun,<br>
And so are you!</p>

<p>Customer Address:<br>
123 Web Dev Lane<br>
Codeville, HTML 54321</p>
```

## Horizontal Rules (`<hr>`) <a name="horizontal-rules"></a>

The `<hr>` element represents a thematic break between paragraph-level elements. It's typically displayed as a horizontal line.

*   It's also an **empty element** (`<hr>` or `<hr />`).
*   Use `<hr>` to indicate a shift in topic or a separation within content.

**Example:**
```html
<h2>Chapter 1: The Beginning</h2>
<p>This is the content of the first chapter...</p>

<hr>

<h2>Chapter 2: The Middle</h2>
<p>This is the content of the second chapter...</p>
```

## Comments in HTML (`<!-- ... -->`) <a name="comments-in-html"></a>

Comments are used to add notes or explanations within your HTML code. They are **not displayed** by the browser.

*   Comments can help you and others understand your code.
*   They can also be used to temporarily hide sections of code during development.
*   A comment starts with `<!--` and ends with `-->`.

**Example:**
```html
<!-- This is a single-line comment -->

<p>This paragraph will be displayed.</p>

<!-- 
  This is a 
  multi-line comment.
  The following heading is currently commented out.
-->

<!-- <h1>Hidden Title</h1> -->
```

**Important**: Do not put sensitive information in HTML comments, as they are still visible to anyone who views the page source code.

## Nesting Elements <a name="nesting-elements"></a>

HTML elements can be nested inside other elements. This means you can place one element within another.

*   Proper nesting is crucial for valid HTML and correct rendering.
*   Elements must be closed in the reverse order they were opened.
    *   **Correct**: `<p>This is <strong>important</strong> text.</p>`
    *   **Incorrect**: `<p>This is <strong>important</p></strong> text.`

**Example of Nesting:**
```html
<div> <!-- A container element -->
    <h2>A Nested Section</h2>
    <p>This paragraph is <em>nested</em> inside the div and contains <strong>strong emphasis</strong>.</p>
</div>
```

## Empty Elements <a name="empty-elements"></a>

As mentioned, some HTML elements are empty (or void) elements. They do not have a closing tag because they don't contain any content between an opening and closing tag.

Common empty elements include:
*   `<br>` (line break)
*   `<hr>` (horizontal rule)
*   `<img>` (image - we'll cover this later)
*   `<input>` (form input field - we'll cover this later)
*   `<link>` (links to external resources like CSS - found in `<head>`)
*   `<meta>` (metadata - found in `<head>`)

In HTML5, you can write them as `<tagname>` or `<tagname />`. Both are valid. For consistency, pick one style and stick with it. Using the self-closing slash (e.g., `<br />`) is a good habit, especially if you ever work with XHTML or XML-based syntaxes.

## Key Takeaways

*   HTML elements are defined by **tags** and can have **attributes** for extra information.
*   **Headings** (`<h1>`-`<h6>`) structure content hierarchically.
*   **Paragraphs** (`<p>`) group blocks of text.
*   **Line breaks** (`<br>`) create new lines within content, while **horizontal rules** (`<hr>`) indicate thematic breaks.
*   **Comments** (`<!-- ... -->`) are for notes and are ignored by the browser.
*   Proper **nesting** of elements is essential.
*   **Empty elements** like `<br>` and `<hr>` don't have closing tags.

With these basic elements, you can start creating more structured and meaningful web content!

---

🔙 [Back to Introduction to HTML](../01-introduction-to-html/README.md) | ➡️ [Next Section: Text Formatting and Lists](../03-text-formatting-and-lists/README.md)
