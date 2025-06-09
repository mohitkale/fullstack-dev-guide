# 3. Text Formatting and Lists

HTML provides a variety of elements to format text and structure content into lists. This section explores common text formatting tags and the different types of lists you can create.

## Table of Contents
1. [Semantic vs. Presentational Formatting](#semantic-vs-presentational-formatting)
2. [Basic Text Formatting](#basic-text-formatting)
   - [Bold Text: `<strong>` and `<b>`](#bold-text)
   - [Italic Text: `<em>` and `<i>`](#italic-text)
   - [Underlined Text: `<u>`](#underlined-text)
   - [Strikethrough Text: `<s>` and `<del>`](#strikethrough-text)
   - [Marked/Highlighted Text: `<mark>`](#marked-text)
3. [Advanced Text Formatting](#advanced-text-formatting)
   - [Subscript: `<sub>`](#subscript)
   - [Superscript: `<sup>`](#superscript)
   - [Preformatted Text: `<pre>`](#preformatted-text)
   - [Code: `<code>`](#code)
   - [Keyboard Input: `<kbd>`](#keyboard-input)
   - [Sample Output: `<samp>`](#sample-output)
   - [Variable: `<var>`](#variable)
4. [Quotations](#quotations)
   - [Blockquotes: `<blockquote>`](#blockquotes)
   - [Inline Quotations: `<q>`](#inline-quotations)
   - [Citations: `<cite>`](#citations)
   - [Abbreviations: `<abbr>`](#abbreviations)
   - [Addresses: `<address>`](#addresses)
5. [Lists](#lists)
   - [Unordered Lists: `<ul>` and `<li>`](#unordered-lists)
   - [Ordered Lists: `<ol>` and `<li>`](#ordered-lists)
   - [Description Lists: `<dl>`, `<dt>`, `<dd>`](#description-lists)
   - [Nested Lists](#nested-lists)
6. [Key Takeaways](#key-takeaways)

## Semantic vs. Presentational Formatting <a name="semantic-vs-presentational-formatting"></a>

When formatting text, HTML5 emphasizes **semantic meaning** over pure presentation.

*   **Semantic elements** describe the *meaning* or *purpose* of the content. For example, `<strong>` indicates that the text is important, while `<em>` indicates emphasis.
*   **Presentational elements** (like `<b>` for bold or `<i>` for italic in older HTML versions) primarily describe how the text should *look*. While still supported, it's generally better to use semantic tags and control visual styling with CSS.

Browsers often render semantic tags with default styling (e.g., `<strong>` as bold, `<em>` as italic), but their primary role is to convey meaning to browsers, search engines, and assistive technologies (like screen readers).

## Basic Text Formatting <a name="basic-text-formatting"></a>

### Bold Text: `<strong>` and `<b>` <a name="bold-text"></a>
*   `<strong>`: Indicates text with strong importance, seriousness, or urgency. Browsers typically render this as bold.
    ```html
    <p><strong>Warning:</strong> This action is irreversible.</p>
    ```
*   `<b>`: Represents text that should be stylistically offset from normal text (like keywords in a document or product names in a review) without conveying any extra importance. Browsers also render this as bold.
    ```html
    <p>The keywords are <b>HTML</b>, <b>CSS</b>, and <b>JavaScript</b>.</p>
    ```
    **Usage**: Prefer `<strong>` when the text has special importance. Use `<b>` for stylistically bold text without added semantic meaning if no other tag is more appropriate.

### Italic Text: `<em>` and `<i>` <a name="italic-text"></a>
*   `<em>` (Emphasis): Indicates text that should be emphasized. Browsers typically render this as italic.
    ```html
    <p>You <em>must</em> complete this step.</p>
    ```
*   `<i>`: Represents text in an alternate voice or mood, or to indicate a technical term, a thought, a ship name, etc. Browsers also render this as italic.
    ```html
    <p>The term <i>hypertext</i> is crucial here.</p>
    <p><i>Queen Anne's Revenge</i> was a famous pirate ship.</p>
    ```
    **Usage**: Prefer `<em>` for emphasis. Use `<i>` for text that is set off from the normal prose for reasons like technical terms, foreign words, or thoughts, if no other tag is more appropriate.

### Underlined Text: `<u>` <a name="underlined-text"></a>
*   `<u>`: Represents text that should be stylistically different from normal text, such as misspelled words or proper names in Chinese. Browsers render this as underlined.
    ```html
    <p>This is an example of <u>underlined</u> text.</p>
    ```
    **Caution**: Avoid using `<u>` for general underlining, as users often associate underlined text with hyperlinks. If you need to underline text for emphasis, consider CSS (`text-decoration: underline;`) instead, but even then, use it sparingly.

### Strikethrough Text: `<s>` and `<del>` <a name="strikethrough-text"></a>
*   `<s>`: Represents content that is no longer accurate or relevant. Browsers typically render this as strikethrough.
    ```html
    <p><s>Price: $99.99</s> Now only $49.99!</p>
    ```
*   `<del>`: Represents deleted text, often used to show edits or changes in a document. Browsers also render this as strikethrough.
    ```html
    <p>My favorite color is <del>blue</del> <ins>red</ins>.</p> 
    <!-- <ins> (inserted text) is often used with <del> -->
    ```

### Marked/Highlighted Text: `<mark>` <a name="marked-text"></a>
*   `<mark>`: Represents text that is marked or highlighted for reference or notation purposes, due to its relevance in another context. Browsers typically render this with a yellow background.
    ```html
    <p>Search results often <mark>highlight</mark> the search terms.</p>
    ```

## Advanced Text Formatting <a name="advanced-text-formatting"></a>

### Subscript: `<sub>` <a name="subscript"></a>
*   `<sub>`: Defines subscripted text, which appears half a character below the normal line and is sometimes rendered in a smaller font.
    ```html
    <p>The chemical formula for water is H<sub>2</sub>O.</p>
    ```

### Superscript: `<sup>` <a name="superscript"></a>
*   `<sup>`: Defines superscripted text, which appears half a character above the normal line and is sometimes rendered in a smaller font.
    ```html
    <p>The equation is E = mc<sup>2</sup>.</p>
    <p>See the 1<sup>st</sup> footnote.</p>
    ```

### Preformatted Text: `<pre>` <a name="preformatted-text"></a>
*   `<pre>`: Defines preformatted text. Text within a `<pre>` element is displayed in a fixed-width font (usually Courier) and preserves both spaces and line breaks.
*   It's ideal for displaying code blocks, poetry, or any text where whitespace and line breaks are significant.
    ```html
    <pre>
    function greet() {
        console.log("Hello, world!");
    }
    </pre>
    ```

### Code: `<code>` <a name="code"></a>
*   `<code>`: Defines a piece of computer code. Browsers usually display it in a monospace font.
*   For a single line or inline code snippet, use `<code>`. For larger blocks of code, nest `<code>` inside `<pre>`.
    ```html
    <p>The HTML tag for a paragraph is <code>&lt;p&gt;</code>.</p>
    <pre><code>
    body {
      font-family: Arial, sans-serif;
    }
    </code></pre>
    ```

### Keyboard Input: `<kbd>` <a name="keyboard-input"></a>
*   `<kbd>`: Represents user input from a keyboard, voice command, or any other text entry device. Browsers typically render it in a monospace font.
    ```html
    <p>To save the file, press <kbd>Ctrl</kbd> + <kbd>S</kbd>.</p>
    ```

### Sample Output: `<samp>` <a name="sample-output"></a>
*   `<samp>`: Represents sample output from a computer program or script.
    ```html
    <p>If you run the script, it will output: <samp>Process completed successfully.</samp></p>
    ```

### Variable: `<var>` <a name="variable"></a>
*   `<var>`: Represents a variable in a mathematical expression or programming context. Often rendered in italics.
    ```html
    <p>In the equation <var>x</var> + <var>y</var> = <var>z</var>, <var>x</var>, <var>y</var>, and <var>z</var> are variables.</p>
    ```

## Quotations <a name="quotations"></a>

### Blockquotes: `<blockquote>` <a name="blockquotes"></a>
*   `<blockquote>`: Defines a section that is quoted from another source. Browsers usually indent `<blockquote>` elements.
*   The `cite` attribute can be used to provide a URL for the source of the quotation.
    ```html
    <blockquote cite="http://www.example.com/source">
        <p>This is a long quotation taken from another source. It will typically be indented by the browser.</p>
    </blockquote>
    ```

### Inline Quotations: `<q>` <a name="inline-quotations"></a>
*   `<q>`: Defines a short inline quotation. Browsers typically surround the quotation with quotation marks.
*   The `cite` attribute can also be used here.
    ```html
    <p>Steve Jobs said, <q>Stay hungry. Stay foolish.</q></p>
    ```

### Citations: `<cite>` <a name="citations"></a>
*   `<cite>`: Defines the title of a creative work (e.g., a book, a song, a movie, a painting, a sculpture). Browsers usually render `<cite>` text in italics.
    ```html
    <p>My favorite book is <cite>The Hitchhiker's Guide to the Galaxy</cite> by Douglas Adams.</p>
    ```

### Abbreviations: `<abbr>` <a name="abbreviations"></a>
*   `<abbr>`: Defines an abbreviation or an acronym.
*   The `title` attribute can be used to show the full expansion of the abbreviation when the user hovers over the element.
    ```html
    <p>The <abbr title="World Health Organization">WHO</abbr> was founded in 1948.</p>
    <p><abbr title="HyperText Markup Language">HTML</abbr> is the standard markup language for creating web pages.</p>
    ```

### Addresses: `<address>` <a name="addresses"></a>
*   `<address>`: Defines contact information for the author/owner of a document or an article. It usually renders in italics and browsers add a line break before and after.
    ```html
    <address>
        Written by John Doe.<br>
        Visit us at:<br>
        Example.com<br>
        Box 564, Disneyland<br>
        USA
    </address>
    ```

## Lists <a name="lists"></a>

HTML offers three main types of lists:

### Unordered Lists: `<ul>` and `<li>` <a name="unordered-lists"></a>
*   An unordered list starts with the `<ul>` tag. Each list item starts with the `<li>` (list item) tag.
*   List items are typically marked with bullets (small black circles) by default.

**Example:**
```html
<ul>
    <li>Coffee</li>
    <li>Tea</li>
    <li>Milk</li>
</ul>
```

### Ordered Lists: `<ol>` and `<li>` <a name="ordered-lists"></a>
*   An ordered list starts with the `<ol>` tag. Each list item also starts with the `<li>` tag.
*   List items are typically marked with numbers by default.
*   The `type` attribute can be used on `<ol>` to change the numbering style:
    *   `type="1"` (numbers - default)
    *   `type="A"` (uppercase letters)
    *   `type="a"` (lowercase letters)
    *   `type="I"` (uppercase Roman numerals)
    *   `type="i"` (lowercase Roman numerals)
*   The `start` attribute can specify the starting number of an ordered list.

**Example:**
```html
<ol type="a" start="3">
    <li>First item (starts at 'c')</li>
    <li>Second item</li>
    <li>Third item</li>
</ol>
```

### Description Lists: `<dl>`, `<dt>`, `<dd>` <a name="description-lists"></a>
*   A description list is a list of terms, with a description of each term.
*   The `<dl>` tag defines the description list.
*   The `<dt>` tag defines the term (name).
*   The `<dd>` tag describes each term.

**Example:**
```html
<dl>
    <dt>HTML</dt>
    <dd>HyperText Markup Language - the standard markup language for creating web pages.</dd>
    <dt>CSS</dt>
    <dd>Cascading Style Sheets - a style sheet language used for describing the presentation of a document written in HTML.</dd>
</dl>
```

### Nested Lists <a name="nested-lists"></a>
*   Lists can be nested within other lists.

**Example:**
```html
<ul>
    <li>Fruit
        <ul>
            <li>Apple</li>
            <li>Banana</li>
            <li>Orange
                <ol type="i">
                    <li>Navel</li>
                    <li>Valencia</li>
                </ol>
            </li>
        </ul>
    </li>
    <li>Vegetables
        <ul>
            <li>Carrot</li>
            <li>Broccoli</li>
        </ul>
    </li>
</ul>
```

## Key Takeaways

*   Prioritize **semantic HTML tags** (`<strong>`, `<em>`) over purely presentational ones (`<b>`, `<i>`) for text formatting.
*   Use `<pre>` for blocks of text where whitespace matters, and `<code>` for inline code snippets.
*   `<blockquote>` and `<q>` are for quotations, while `<cite>` is for titles of works.
*   `<abbr>` provides expansions for abbreviations/acronyms.
*   HTML supports **unordered lists** (`<ul>`), **ordered lists** (`<ol>`), and **description lists** (`<dl>`).
*   List items are defined with `<li>` (for `<ul>` and `<ol>`) or `<dt>`/`<dd>` (for `<dl>`).
*   Lists can be **nested** to create complex hierarchical structures.

Mastering these text formatting and list elements will allow you to structure and present your web content more effectively and semantically.

---

🔙 [Back to Basic HTML Elements](../02-basic-html-elements/README.md) | ➡️ [Next Section: Links and Images](../04-links-and-images/README.md)
