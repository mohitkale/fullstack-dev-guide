# 6. CSS Units and Values

Understanding the different units and value types in CSS is fundamental to effectively styling web pages. CSS properties accept various kinds of values, from simple keywords to complex measurements and functions. This module explores these units and values, helping you make informed choices for layout, typography, and more.

## Table of Contents
1.  [Introduction to CSS Values](#intro-css-values)
2.  [Common CSS Data Types](#common-data-types)
    *   [Keywords](#keywords)
    *   [Numbers and Integers](#numbers-integers)
    *   [Strings](#strings)
    *   [URLs](#urls)
    *   [Colors](#colors-recap)
    *   [Images (and Gradients)](#images-gradients-recap)
    *   [Functions](#functions)
3.  [Length Units](#length-units)
    *   [Absolute Length Units](#absolute-lengths)
        *   [`px` (Pixels)](#px)
        *   [`pt` (Points), `pc` (Picas)](#pt-pc)
        *   [`in` (Inches), `cm` (Centimeters), `mm` (Millimeters)](#in-cm-mm)
    *   [Relative Length Units](#relative-lengths)
        *   [`em`](#em)
        *   [`rem` (Root em)](#rem)
        *   [`%` (Percentage)](#percentage)
        *   [`vw` (Viewport Width)](#vw)
        *   [`vh` (Viewport Height)](#vh)
        *   [`vmin` (Viewport Minimum)](#vmin)
        *   [`vmax` (Viewport Maximum)](#vmax)
        *   [`ex` (x-height)](#ex)
        *   [`ch` (character width)](#ch)
4.  [Angle Units](#angle-units)
    *   [`deg` (Degrees)](#deg)
    *   [`grad` (Gradians)](#grad)
    *   [`rad` (Radians)](#rad)
    *   [`turn`](#turn)
5.  [Time Units](#time-units)
    *   [`s` (Seconds)](#s)
    *   [`ms` (Milliseconds)](#ms)
6.  [Frequency Units](#frequency-units)
    *   [`Hz` (Hertz)](#hz)
    *   [`kHz` (Kilohertz)](#khz)
7.  [Resolution Units](#resolution-units)
    *   [`dpi` (Dots Per Inch)](#dpi)
    *   [`dpcm` (Dots Per Centimeter)](#dpcm)
    *   [`dppx` (Dots Per Pixel)](#dppx)
8.  [Calculation (`calc()`) Function](#calc-function)
9.  [Attribute (`attr()`) Function](#attr-function)
10. [Choosing the Right Units](#choosing-units)
11. [Key Takeaways](#key-takeaways)

## 1. Introduction to CSS Values <a name="intro-css-values"></a>

Every CSS property has a defined set of valid value types. For example, the `color` property accepts color values (like keywords, HEX, RGB), while `font-size` accepts length units or keywords, and `background-image` accepts URLs or image functions (like gradients).

## 2. Common CSS Data Types <a name="common-data-types"></a>

### Keywords <a name="keywords"></a>
*   Predefined, human-readable identifiers.
*   Examples: `auto`, `inherit`, `initial`, `unset`, `none`, `solid`, `block`, `absolute`, `center`, `red`, `transparent`.
    ```css
    div {
      display: block;
      border-style: solid;
      color: red;
      margin: auto;
    }
    ```

### Numbers and Integers <a name="numbers-integers"></a>
*   **Numbers**: Can be integers or have decimal points (e.g., `10`, `3.14`, `-5`). Used in properties like `opacity`, `line-height` (unitless), `order`.
*   **Integers**: Whole numbers (e.g., `1`, `100`, `-5`). Used in properties like `z-index`, `column-count`.
    ```css
    .element {
      opacity: 0.8;
      line-height: 1.5; /* Number (unitless) */
      z-index: 10;      /* Integer */
    }
    ```

### Strings <a name="strings"></a>
*   Sequences of characters enclosed in single (`'`) or double (`"`) quotes.
*   Used in properties like `content` (for pseudo-elements), `font-family` (for font names with spaces), `quotes`.
    ```css
    p::before {
      content: "Note: ";
    }
    body {
      font-family: "Times New Roman", serif;
    }
    ```

### URLs <a name="urls"></a>
*   A pointer to a resource, typically an image, font, or another stylesheet.
*   Specified using the `url()` function.
    ```css
    body {
      background-image: url("images/background.jpg");
    }
    @font-face {
      font-family: 'MyFont';
      src: url('../fonts/myfont.woff2');
    }
    ```

### Colors <a name="colors-recap"></a>
*   Covered in detail in the "CSS Colors and Backgrounds" module.
*   Formats: keywords (`red`), HEX (`#FF0000`), RGB/RGBA (`rgb(255,0,0)`), HSL/HSLA (`hsl(0, 100%, 50%)`).

### Images (and Gradients) <a name="images-gradients-recap"></a>
*   Values that represent a 2D image.
*   Can be a URL to an image file (`url()`) or a CSS-generated image like a gradient (`linear-gradient()`, `radial-gradient()`).
*   Used in properties like `background-image`, `list-style-image`, `border-image-source`.

### Functions <a name="functions"></a>
*   CSS values that are processed to return another value. They take arguments.
*   Examples: `url()`, `rgb()`, `hsl()`, `calc()`, `attr()`, `var()` (for CSS Custom Properties), `linear-gradient()`.
    ```css
    :root {
      --main-color: blue;
    }
    .element {
      width: calc(100% - 20px);
      color: var(--main-color);
      background-image: linear-gradient(to right, white, var(--main-color));
    }
    ```

## 3. Length Units <a name="length-units"></a>

Length units are used to specify sizes, distances, padding, margins, border widths, etc.

### Absolute Length Units <a name="absolute-lengths"></a>
*   Fixed units; their size does not change based on other elements or viewport dimensions.
*   Generally recommended less for screen media due to lack of scalability, but can be useful for print or when precise, fixed dimensions are needed.

    #### `px` (Pixels) <a name="px"></a>
    *   The most common absolute unit for screen media.
    *   Represents one device pixel (dot) on a display.
    *   On high-resolution (HiDPI or Retina) displays, one CSS `px` might correspond to multiple device pixels to maintain visual consistency.
        ```css
        div {
          width: 200px;
          font-size: 16px;
          border: 1px solid black;
        }
        ```

    #### `pt` (Points), `pc` (Picas) <a name="pt-pc"></a>
    *   **`pt` (Points)**: 1pt = 1/72 of an inch. Commonly used in print typography.
    *   **`pc` (Picas)**: 1pc = 12pt.
        ```css
        @media print {
          body {
            font-size: 12pt;
          }
        }
        ```

    #### `in` (Inches), `cm` (Centimeters), `mm` (Millimeters) <a name="in-cm-mm"></a>
    *   Physical units. `1in = 2.54cm = 25.4mm = 96px` (by CSS definition).
    *   More useful for print stylesheets than screen.
        ```css
        .paper {
          width: 8.5in;
          height: 11in;
        }
        ```

### Relative Length Units <a name="relative-lengths"></a>
*   Their size is relative to another length property, making them flexible and scalable.

    #### `em` <a name="em"></a>
    *   Relative to the `font-size` of the element itself (for properties other than `font-size`).
    *   For the `font-size` property, `em` is relative to the `font-size` of the parent element.
    *   Can lead to compounding effects in nested elements if not managed carefully.
        ```css
        div {
          font-size: 16px;
          padding: 1em; /* 1 * 16px = 16px */
        }
        div p {
          font-size: 1.2em; /* 1.2 * 16px (parent's font-size) = 19.2px */
          margin-left: 2em; /* 2 * 19.2px (this element's font-size) = 38.4px */
        }
        ```

    #### `rem` (Root em) <a name="rem"></a>
    *   Relative to the `font-size` of the root element (`<html>`).
    *   Avoids the compounding issue of `em` units, making it easier to manage consistent sizing across a site.
    *   **Highly recommended for font sizes and often for spacing/layout for better accessibility and scalability.**
        ```css
        html {
          font-size: 16px; /* Base font size */
        }
        h1 {
          font-size: 2rem; /* 2 * 16px = 32px */
        }
        p {
          font-size: 1rem; /* 1 * 16px = 16px */
          padding: 1rem;   /* 1 * 16px = 16px */
        }
        ```

    #### `%` (Percentage) <a name="percentage"></a>
    *   Relative to another value, usually the same property of the parent element.
    *   For `width`, `height`, `padding`, `margin` (horizontal): relative to the width of the containing block.
    *   For `font-size`: relative to the parent element's `font-size`.
    *   For `line-height`: relative to the element's own `font-size`.
        ```css
        .parent {
          width: 500px;
          font-size: 20px;
        }
        .child {
          width: 50%; /* 50% of 500px = 250px */
          font-size: 80%; /* 80% of 20px = 16px */
          padding: 10%; /* 10% of 500px (parent width) = 50px */
        }
        ```

    #### `vw` (Viewport Width) <a name="vw"></a>
    *   1vw = 1% of the viewport's width.

    #### `vh` (Viewport Height) <a name="vh"></a>
    *   1vh = 1% of the viewport's height.

    #### `vmin` (Viewport Minimum) <a name="vmin"></a>
    *   1vmin = 1% of the smaller of the viewport's width or height.

    #### `vmax` (Viewport Maximum) <a name="vmax"></a>
    *   1vmax = 1% of the larger of the viewport's width or height.
        ```css
        .hero-section {
          height: 100vh; /* Full viewport height */
          font-size: 5vw; /* Font size scales with viewport width */
        }
        .square {
          width: 50vmin;
          height: 50vmin; /* A square that stays within viewport */
        }
        ```
    *   Viewport units are excellent for creating elements that scale proportionally to the browser window.

    #### `ex` (x-height) <a name="ex"></a>
    *   Relative to the x-height of the current font (the height of the lowercase 'x').
    *   Rarely used but can be useful for fine-tuning vertical alignment with text.

    #### `ch` (character width) <a name="ch"></a>
    *   Relative to the width of the "0" (zero) glyph in the current font.
    *   Useful for setting widths based on character counts, e.g., for optimal line length.
        ```css
        p {
          max-width: 60ch; /* Aim for around 60 characters per line */
        }
        ```

## 4. Angle Units <a name="angle-units"></a>
*   Used in properties like `transform: rotate()` and in gradient definitions.

    #### `deg` (Degrees) <a name="deg"></a>
    *   A full circle is 360deg.

    #### `grad` (Gradians) <a name="grad"></a>
    *   A full circle is 400grad.

    #### `rad` (Radians) <a name="rad"></a>
    *   A full circle is 2π radians (approximately 6.2832rad).

    #### `turn` <a name="turn"></a>
    *   A full circle is 1turn.
        ```css
        .rotate-me {
          transform: rotate(45deg);
        }
        .gradient-angle {
          background: linear-gradient(0.25turn, red, blue); /* 0.25turn = 90deg */
        }
        ```

## 5. Time Units <a name="time-units"></a>
*   Used for CSS Animations and Transitions duration/delay.

    #### `s` (Seconds) <a name="s"></a>

    #### `ms` (Milliseconds) <a name="ms"></a>
    *   1s = 1000ms.
        ```css
        .animated-box {
          transition-property: background-color;
          transition-duration: 0.5s; /* or 500ms */
          transition-delay: 100ms;
        }
        ```

## 6. Frequency Units <a name="frequency-units"></a>
*   Used for aural stylesheets (less common).

    #### `Hz` (Hertz)

    #### `kHz` (Kilohertz)
    *   1kHz = 1000Hz.

## 7. Resolution Units <a name="resolution-units"></a>
*   Used in media queries to target devices based on screen resolution.

    #### `dpi` (Dots Per Inch)

    #### `dpcm` (Dots Per Centimeter)

    #### `dppx` (Dots Per Pixel)
    *   `1dppx` = `96dpi`.
        ```css
        @media (min-resolution: 2dppx) {
          /* Styles for high-resolution (Retina) displays */
        }
        @media print and (min-resolution: 300dpi) {
          /* High-quality print styles */
        }
        ```

## 8. Calculation (`calc()`) Function <a name="calc-function"></a>
*   Allows you to perform mathematical calculations (+, -, *, /) using different units directly in CSS.
*   Extremely useful for responsive design and complex layouts.
    ```css
    .container {
      width: calc(100% - 40px); /* Full width minus 40px for padding/margins */
    }
    .sidebar {
      width: 300px;
    }
    .main-content {
      width: calc(100% - 300px - 2em); /* Subtract sidebar width and some margin */
    }
    .element {
      font-size: calc(1rem + 0.5vw); /* Responsive font size */
    }
    ```
*   **Note**: For addition (`+`) and subtraction (`-`), whitespace around the operator is required. For multiplication (`*`) and division (`/`), it's not required but recommended for readability.

## 9. Attribute (`attr()`) Function <a name="attr-function"></a>
*   Retrieves the value of an HTML attribute of the selected element and uses it in CSS.
*   Most commonly used with the `content` property for pseudo-elements.
*   Support for `attr()` with other properties or for type conversion (e.g., `attr(data-width px)`) is limited across browsers.
    ```html
    <a href="#" data-tooltip="Click me!">Link</a>
    ```
    ```css
    a::after {
      content: " (" attr(href) ")"; /* Displays ( # ) after the link text */
    }
    a[data-tooltip]::before {
      content: attr(data-tooltip);
      /* Further styling for tooltip appearance */
    }
    ```

## 10. Choosing the Right Units <a name="choosing-units"></a>

*   **Font Sizes**: Use `rem` for base font sizes and components to ensure scalability and accessibility. `em` can be useful for components where sizing should be relative to the component's own font size.
*   **Layout (Widths, Heights, Margins, Padding)**:
    *   `%` for fluid layouts that adapt to container size.
    *   `px` for fixed-size elements or when precise pixel control is needed (e.g., borders, small icons).
    *   `rem` for consistent spacing that scales with the root font size.
    *   `vw`/`vh` for elements that need to scale directly with the viewport (e.g., full-screen hero sections, responsive typography).
    *   `calc()` to combine different units for complex sizing.
*   **Borders**: `px` is common for crisp, thin borders.
*   **Media Queries**: `em` or `rem` are often preferred over `px` for breakpoints to make them adapt if the user changes their base font size.
*   **Print**: `pt`, `cm`, `in` are suitable for print stylesheets.

## 11. Key Takeaways <a name="key-takeaways"></a>

*   CSS properties accept specific data types: keywords, numbers, strings, URLs, colors, images, and functions.
*   Length units are either **absolute** (`px`, `pt`) or **relative** (`em`, `rem`, `%`, `vw`, `vh`).
*   Relative units are generally preferred for responsive and accessible web design.
*   `rem` is excellent for font sizes and scalable spacing.
*   Viewport units (`vw`, `vh`, `vmin`, `vmax`) allow sizing relative to the browser window.
*   The `calc()` function is powerful for mixing units and creating dynamic layouts.
*   Angle, time, frequency, and resolution units serve specific purposes in transformations, animations, aural styles, and media queries.
*   Choosing appropriate units depends on the context and desired behavior (fixed, scalable, relative to parent, relative to viewport).

Understanding and correctly applying CSS units and values is crucial for building robust, responsive, and accessible websites.

---

⬅️ [Back to CSS Typography](../05-css-typography/README.md) | ➡️ [Next Section: The CSS Display Property](../07-css-display-property/README.md)
