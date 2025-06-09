# 5. CSS Typography

Typography is a critical aspect of web design, influencing readability, user experience, and the overall aesthetic of a website. CSS provides a rich set of properties to control how text is displayed, from choosing fonts to adjusting spacing and alignment.

## Table of Contents
1.  [Font Properties](#font-properties)
    *   [`font-family`](#font-family)
    *   [`font-size`](#font-size)
    *   [`font-weight`](#font-weight)
    *   [`font-style`](#font-style)
    *   [`font-variant`](#font-variant)
    *   [`line-height`](#line-height)
    *   [Shorthand `font` Property](#shorthand-font)
2.  [Text Properties](#text-properties)
    *   [`text-align`](#text-align)
    *   [`text-decoration`](#text-decoration)
    *   [`text-transform`](#text-transform)
    *   [`text-indent`](#text-indent)
    *   [`letter-spacing`](#letter-spacing)
    *   [`word-spacing`](#word-spacing)
    *   [`white-space`](#white-space)
    *   [`text-shadow`](#text-shadow)
    *   [`text-overflow`](#text-overflow)
3.  [Web Fonts (`@font-face`)](#web-fonts)
    *   [Using `@font-face`](#using-font-face)
    *   [Font Formats (WOFF, WOFF2, TTF, OTF)](#font-formats)
    *   [Font Loading Strategies & Performance](#font-loading)
4.  [Variable Fonts](#variable-fonts)
5.  [Units for Typography](#typography-units)
    *   [Absolute Units (`px`, `pt`)](#absolute-units)
    *   [Relative Units (`em`, `rem`, `%`, `vw`, `vh`)](#relative-units)
6.  [Accessibility Considerations for Typography](#typography-accessibility)
7.  [Key Takeaways](#key-takeaways)

## 1. Font Properties <a name="font-properties"></a>

These properties directly affect the font itself.

### `font-family` <a name="font-family"></a>
*   Specifies a prioritized list of font family names and/or generic family names for an element.
*   The browser will use the first font in the list that it has installed or can download.
*   Always end the list with a generic font family (e.g., `serif`, `sans-serif`, `monospace`, `cursive`, `fantasy`) as a fallback.
*   Font names containing spaces must be quoted (e.g., `"Times New Roman"`).
    ```css
    body {
      font-family: Arial, Helvetica, sans-serif;
    }
    h1 {
      font-family: "Georgia", serif;
    }
    code {
      font-family: "Courier New", Courier, monospace;
    }
    ```

### `font-size` <a name="font-size"></a>
*   Sets the size of the font.
*   Can be an absolute size (e.g., `px`, `pt`), a relative size (e.g., `em`, `rem`, `%`), or a keyword (e.g., `medium`, `small`, `large`).
    ```css
    p {
      font-size: 16px; /* Absolute size */
    }
    h1 {
      font-size: 2em; /* Relative to parent's font size */
    }
    .small-text {
      font-size: 0.8rem; /* Relative to root element's font size */
    }
    ```

### `font-weight` <a name="font-weight"></a>
*   Sets the thickness or boldness of the font.
*   Values:
    *   Keywords: `normal` (default, usually 400), `bold` (usually 700).
    *   Numeric values: `100`, `200`, ..., `900` (in increments of 100). `400` is normal, `700` is bold.
    *   Relative keywords: `lighter`, `bolder` (relative to the parent element's font weight).
    ```css
    strong, b {
      font-weight: bold; /* or 700 */
    }
    .light-text {
      font-weight: 300;
    }
    h2 {
      font-weight: 600; /* Semi-bold */
    }
    ```
    *Note: Not all fonts support all numeric weights. The browser will pick the closest available weight.*

### `font-style` <a name="font-style"></a>
*   Sets the style of the font.
*   Values:
    *   `normal`: (Default) Selects the normal version of the font.
    *   `italic`: Selects the italic version of the font. If not available, the browser may simulate it by slanting (obliquing) the normal version.
    *   `oblique`: Selects an oblique version of the font (a slanted version of the normal font). You can optionally specify an angle (e.g., `oblique 10deg`).
    ```css
    em, i {
      font-style: italic;
    }
    .quote {
      font-style: italic;
    }
    ```

### `font-variant` <a name="font-variant"></a>
*   Allows you to select small-caps variations of the font.
*   Common value: `small-caps` (displays lowercase letters as smaller uppercase letters).
    ```css
    .title-variant {
      font-variant: small-caps;
    }
    ```
    *CSS Fonts Module Level 3 introduces more granular control with properties like `font-variant-caps`, `font-variant-numeric`, etc., but `font-variant: small-caps;` is widely supported.*

### `line-height` <a name="line-height"></a>
*   Sets the height of a line box. It's the distance from the top of one line of text to the top of the next.
*   Can be a number (multiplied by the element's font size), a length (`px`, `em`), a percentage (relative to the element's font size), or `normal` (default, browser-dependent, usually around 1.2).
*   Using a unitless number is often recommended for `line-height` as it allows descendant elements to inherit the ratio and compute their own line height based on their font size.
    ```css
    p {
      line-height: 1.6; /* Recommended: 1.6 times the font-size of p */
    }
    h1 {
      line-height: 1.2;
    }
    .custom-line-height {
      font-size: 14px;
      line-height: 24px; /* Fixed line height */
    }
    ```

### Shorthand `font` Property <a name="shorthand-font"></a>
*   A shorthand property for setting `font-style`, `font-variant`, `font-weight`, `font-size`, `line-height`, and `font-family` in a single declaration.
*   **Syntax Order**: `font: [font-style] [font-variant] [font-weight] font-size[/line-height] font-family;`
*   `font-size` and `font-family` are **required**.
*   If `line-height` is specified, it must follow `font-size`, separated by a `/`.
*   If any optional values are omitted, they are set to their initial (default) values.
    ```css
    body {
      font: italic small-caps bold 16px/1.5 Arial, sans-serif;
    }
    p {
      font: 1em/1.6 "Helvetica Neue", Helvetica, Arial, sans-serif;
    }
    h1 {
      font: bold 2.5rem "Times New Roman", serif;
    }
    ```
    *Using the `font` shorthand will reset any unspecified font properties to their initial values. For example, if you set `font-weight` separately and then use `font: 16px Arial, sans-serif;`, the `font-weight` will be reset to `normal`.*

## 2. Text Properties <a name="text-properties"></a>

These properties control the appearance and layout of text content within an element.

### `text-align` <a name="text-align"></a>
*   Specifies the horizontal alignment of inline-level content (like text) within a block-level parent element.
*   Values:
    *   `left`: (Default for LTR languages) Aligns text to the left.
    *   `right`: (Default for RTL languages) Aligns text to the right.
    *   `center`: Centers the text.
    *   `justify`: Stretches lines of text so that each line has equal width (except possibly the last line), creating straight edges on both sides.
    *   `start`: Aligns to the start edge of the line box (logical equivalent of `left` in LTR, `right` in RTL).
    *   `end`: Aligns to the end edge of the line box (logical equivalent of `right` in LTR, `left` in RTL).
    ```css
    p {
      text-align: justify;
    }
    h1 {
      text-align: center;
    }
    .caption {
      text-align: right;
    }
    ```

### `text-decoration` <a name="text-decoration"></a>
*   Specifies decorations added to text, such as underlines, overlines, or line-throughs.
*   It's a shorthand for `text-decoration-line`, `text-decoration-color`, `text-decoration-style`, and `text-decoration-thickness`.
*   Common `text-decoration-line` values:
    *   `none`: (Default) No decoration.
    *   `underline`: Adds a line below the text.
    *   `overline`: Adds a line above the text.
    *   `line-through`: Adds a line through the middle of the text.
    ```css
    a {
      text-decoration: underline; /* Default for links */
    }
    a:hover {
      text-decoration: none; /* Remove underline on hover */
    }
    .strikethrough {
      text-decoration: line-through red wavy 2px;
      /* text-decoration-line: line-through; */
      /* text-decoration-color: red; */
      /* text-decoration-style: wavy; */
      /* text-decoration-thickness: 2px; */
    }
    ```

### `text-transform` <a name="text-transform"></a>
*   Controls the capitalization of text.
*   Values:
    *   `none`: (Default) No transformation.
    *   `capitalize`: Transforms the first letter of each word to uppercase.
    *   `uppercase`: Transforms all letters to uppercase.
    *   `lowercase`: Transforms all letters to lowercase.
    ```css
    h1 {
      text-transform: uppercase;
    }
    .button-label {
      text-transform: capitalize;
    }
    ```

### `text-indent` <a name="text-indent"></a>
*   Specifies the indentation of the first line of text in a block-level element.
*   Can be a length (`px`, `em`) or a percentage (of the containing block's width).
    ```css
    p {
      text-indent: 2em; /* Indent first line by 2 times the font size */
    }
    ```

### `letter-spacing` <a name="letter-spacing"></a>
*   Increases or decreases the space between characters in text.
*   Can be a length (`px`, `em`). `normal` is the default.
    ```css
    .heading-spaced {
      letter-spacing: 2px;
    }
    .condensed-text {
      letter-spacing: -0.05em;
    }
    ```

### `word-spacing` <a name="word-spacing"></a>
*   Increases or decreases the space between words.
*   Can be a length (`px`, `em`). `normal` is the default.
    ```css
    .wide-words {
      word-spacing: 0.5em;
    }
    ```

### `white-space` <a name="white-space"></a>
*   Controls how white space inside an element is handled (e.g., spaces, tabs, newlines).
*   Values:
    *   `normal`: (Default) Collapses multiple white spaces into one, breaks lines as needed to fill line boxes.
    *   `nowrap`: Collapses white space but prevents text from wrapping to the next line (text may overflow).
    *   `pre`: Preserves white space and newlines; text wraps only at explicit line breaks (`<br>`).
    *   `pre-wrap`: Preserves white space and newlines; text wraps when necessary and at explicit line breaks.
    *   `pre-line`: Collapses sequences of white space but preserves newlines.
    *   `break-spaces`: Similar to `pre-wrap` but any sequence of preserved white space always takes up space, including at the end of the line.
    ```css
    code, pre {
      white-space: pre-wrap; /* Good for code blocks */
    }
    .no-wrap-text {
      white-space: nowrap;
    }
    ```

### `text-shadow` <a name="text-shadow"></a>
*   Adds shadow effects to text.
*   Syntax: `text-shadow: h-offset v-offset [blur-radius] [color];`
    *   `h-offset`: Horizontal offset (positive is right, negative is left).
    *   `v-offset`: Vertical offset (positive is down, negative is up).
    *   `blur-radius`: (Optional) The blur distance. Larger values mean more blur.
    *   `color`: (Optional) The color of the shadow. If omitted, it often uses the element's `color`.
*   You can specify multiple shadows, comma-separated.
    ```css
    .fancy-heading {
      text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
    }
    .retro-text {
      color: white;
      text-shadow: 1px 1px 0 #000, -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000;
    }
    ```

### `text-overflow` <a name="text-overflow"></a>
*   Specifies how to signal truncated text to the user when text overflows its container.
*   Typically used with `overflow: hidden;` and `white-space: nowrap;`.
*   Values:
    *   `clip`: (Default) Truncates the text at the container's edge.
    *   `ellipsis`: Displays an ellipsis (`…`) to represent clipped text.
    ```css
    .truncate-text {
      width: 200px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
    }
    ```

## 3. Web Fonts (`@font-face`) <a name="web-fonts"></a>

*   The `@font-face` CSS at-rule allows you to load custom fonts onto a webpage. This means you are not limited to the fonts installed on a user's computer.

### Using `@font-face` <a name="using-font-face"></a>
*   You define a custom font family name and point to the font file(s).
    ```css
    @font-face {
      font-family: 'MyCustomFont'; /* Name you'll use in font-family properties */
      src: url('path/to/mycustomfont.woff2') format('woff2'), /* Modern format */
           url('path/to/mycustomfont.woff') format('woff');   /* Older format */
      font-weight: normal; /* Optional: specify weight for this font file */
      font-style: normal;  /* Optional: specify style for this font file */
    }

    body {
      font-family: 'MyCustomFont', sans-serif;
    }
    ```
*   You can have multiple `@font-face` rules for different weights or styles of the same font family.
    ```css
    /* Regular weight */
    @font-face {
      font-family: 'OpenSans';
      src: url('opensans-regular.woff2') format('woff2');
      font-weight: 400; /* or normal */
      font-style: normal;
    }

    /* Bold weight */
    @font-face {
      font-family: 'OpenSans';
      src: url('opensans-bold.woff2') format('woff2');
      font-weight: 700; /* or bold */
      font-style: normal;
    }

    h1 {
      font-family: 'OpenSans', sans-serif;
      font-weight: 700; /* Will use opensans-bold.woff2 */
    }
    p {
      font-family: 'OpenSans', sans-serif;
      font-weight: 400; /* Will use opensans-regular.woff2 */
    }
    ```

### Font Formats (WOFF, WOFF2, TTF, OTF) <a name="font-formats"></a>
*   **WOFF2 (Web Open Font Format 2)**: Best compression, modern browsers. Should be listed first.
*   **WOFF (Web Open Font Format)**: Good compression, widely supported.
*   **TTF (TrueType Font) / OTF (OpenType Font)**: Older formats, less compression. Can be used as fallbacks if needed, but WOFF/WOFF2 are preferred for web use.
*   **EOT (Embedded OpenType)**: For very old IE versions (IE6-8). Rarely needed today.
*   **SVG Fonts**: For older iOS versions. Rarely needed today.

### Font Loading Strategies & Performance <a name="font-loading"></a>
*   **FOUT (Flash of Unstyled Text)**: Text is initially displayed with a fallback font, then swaps to the web font once loaded.
*   **FOIT (Flash of Invisible Text)**: Text is hidden until the web font loads. Can be bad for UX if loading is slow.
*   The `font-display` descriptor within `@font-face` gives you control over this behavior:
    *   `auto`: Browser default (often like `block`).
    *   `block`: Short invisible period, then swap (FOIT, then FOUT).
    *   `swap`: Very short invisible period, then fallback, then swap (FOUT).
    *   `fallback`: Very short invisible period, then fallback. If font doesn't load quickly, uses fallback permanently for that page load.
    *   `optional`: Very short invisible period. If font isn't available quickly, uses fallback and doesn't try to download for that page load.
    ```css
    @font-face {
      font-family: 'MyAwesomeFont';
      src: url('myawesomefont.woff2') format('woff2');
      font-display: swap; /* Recommended for good perceived performance */
    }
    ```
*   **Preloading fonts**: Use `<link rel="preload" href="/path/to/font.woff2" as="font" type="font/woff2" crossorigin>` in your HTML `<head>` to start loading important fonts sooner.
*   **Self-hosting vs. Font Services**: You can host font files on your own server or use services like Google Fonts or Adobe Fonts, which handle hosting and provide CSS snippets.

## 4. Variable Fonts <a name="variable-fonts"></a>

*   Variable fonts are an evolution of OpenType fonts, allowing multiple variations of a typeface to be incorporated into a single font file.
*   Instead of separate files for bold, italic, light, condensed, etc., a variable font can contain all these variations (and many more) along a continuous range of design axes (e.g., weight, width, slant, optical size).
*   You control these axes using low-level CSS properties like `font-variation-settings` or higher-level properties as they become standardized (e.g., `font-weight` can accept a wider range of values for variable fonts).
    ```css
    @font-face {
      font-family: 'MyVariableFont';
      src: url('myvariablefont.woff2') format('woff2-variations'); /* Note format */
      font-weight: 100 900; /* Declare supported weight range */
      font-style: normal oblique 0deg 20deg; /* Declare supported slant range */
    }

    .variable-text {
      font-family: 'MyVariableFont', sans-serif;
      font-weight: 550; /* A specific weight */
      font-variation-settings: 'wdth' 75, 'slnt' -10; /* Custom axis values: width 75%, slant -10deg */
    }
    ```
*   Variable fonts can offer significant file size savings and greater design flexibility.

## 5. Units for Typography <a name="typography-units"></a>

Choosing the right units for font sizes, line heights, and spacing is crucial for responsive and accessible typography.

### Absolute Units (`px`, `pt`) <a name="absolute-units"></a>
*   `px` (pixels): Fixed-size units. One pixel is one dot on the screen (though this can be more complex with high-resolution displays).
*   `pt` (points): Traditionally used in print (1pt = 1/72 of an inch). Less common for screen design but can be used.
*   Absolute units are not scalable based on user preferences or parent element sizes, which can be an accessibility concern.

### Relative Units (`em`, `rem`, `%`, `vw`, `vh`) <a name="relative-units"></a>
*   `em`: Relative to the `font-size` of the parent element (for `font-size` itself, it's relative to the inherited font size). If used for other properties like `padding` or `margin`, it's relative to the `font-size` of the element itself.
    *   `2em` means twice the size of the parent's font.
*   `rem` (root em): Relative to the `font-size` of the root element (`<html>`). This avoids compounding issues that `em` can have with deeply nested elements.
    *   `2rem` means twice the size of the `<html>` element's font.
    *   **Often the preferred unit for font sizes for better accessibility and scalability.**
*   `%` (percentage): Relative to the parent element's property value. For `font-size`, it's relative to the parent's `font-size`.
*   `vw` (viewport width): 1vw = 1% of the viewport width.
*   `vh` (viewport height): 1vh = 1% of the viewport height.
    *   `vw` and `vh` can be used for responsive typography that scales with the viewport size, but should be used carefully, often with `calc()` and min/max font sizes to maintain readability.

## 6. Accessibility Considerations for Typography <a name="typography-accessibility"></a>

*   **Readability**: Choose clear, legible fonts. Avoid overly decorative or script fonts for body text.
*   **Sufficient Font Size**: Ensure body text is large enough to be easily read (e.g., a minimum of `16px` is a common recommendation for base font size).
*   **Line Length**: Aim for an optimal line length (around 45-75 characters per line) for comfortable reading.
*   **Line Height**: Adequate `line-height` (e.g., 1.5 to 1.8) improves readability, especially for large blocks of text.
*   **Scalability**: Use relative units like `rem` for font sizes to allow users to scale text via browser settings.
*   **Contrast**: Ensure sufficient contrast between text color and background color (covered in Colors & Backgrounds module).

## 7. Key Takeaways <a name="key-takeaways"></a>

*   CSS offers a wide array of `font-*` properties (e.g., `font-family`, `font-size`, `font-weight`) and `text-*` properties (e.g., `text-align`, `text-decoration`, `letter-spacing`) to control typography.
*   The `font` shorthand property can set multiple font attributes concisely.
*   `@font-face` enables the use of custom web fonts, expanding design possibilities beyond system fonts.
*   Understanding font formats (WOFF2, WOFF) and loading strategies (`font-display`) is important for performance.
*   Variable fonts offer flexibility and file size benefits.
*   Choosing appropriate units (`px`, `em`, `rem`) is crucial for responsive and accessible text.
*   Always prioritize readability and accessibility in your typographic choices.

Mastering CSS typography is essential for creating professional, engaging, and readable web content.

---

⬅️ [Back to CSS Colors and Backgrounds](../04-css-colors-and-backgrounds/README.md) | ➡️ [Next Section: CSS Units and Values](../06-css-units-and-values/README.md)
