# 15. CSS Preprocessors (Sass/SCSS)

CSS preprocessors are scripting languages that extend the default capabilities of CSS. They allow you to use logic in your CSS code, such as variables, nesting, mixins, functions, and more, which makes CSS more maintainable, themeable, and extendable. The preprocessor takes code written in its specific syntax and compiles it into regular CSS that browsers can understand.

Sass (Syntactically Awesome StyleSheets) is one of the most popular and mature CSS preprocessors. It has two syntaxes:

1.  **Sass (Indented Syntax)**: Uses indentation rather than brackets to separate code blocks and newlines rather than semicolons to separate rules. Files use the `.sass` extension.
2.  **SCSS (Sassy CSS)**: Uses CSS-like syntax with curly braces and semicolons. It's a superset of CSS, meaning any valid CSS is also valid SCSS. Files use the `.scss` extension. SCSS is generally more popular due to its similarity to standard CSS.

This tutorial will focus on the **SCSS syntax**.

## Table of Contents
1.  [Why Use a CSS Preprocessor?](#why-use-preprocessor)
2.  [Setting Up Sass/SCSS](#setting-up-sass)
    *   [Using Node.js (npm)](#setup-npm)
    *   [Command Line Compilation](#cli-compilation)
    *   [GUI Applications](#gui-apps)
    *   [Build Tool Integration (Webpack, Gulp, Parcel)](#build-tools)
3.  [SCSS Syntax Basics](#scss-syntax-basics)
4.  [Variables](#variables-scss)
    *   [Declaring Variables (`$`)](#declaring-variables)
    *   [Variable Scope](#variable-scope)
    *   [Variable Defaults (`!default`)](#variable-defaults)
5.  [Nesting](#nesting-scss)
    *   [Nesting Selectors](#nesting-selectors)
    *   [Parent Selector (`&`)](#parent-selector)
    *   [Nesting Properties](#nesting-properties)
    *   [Caution with Over-Nesting](#caution-nesting)
6.  [Partials and Imports](#partials-imports-scss)
    *   [Partials (`_filename.scss`)](#partials-scss)
    *   [`@import` Directive](#import-directive-scss)
7.  [Mixins](#mixins-scss)
    *   [Defining a Mixin (`@mixin`)](#defining-mixin)
    *   [Including a Mixin (`@include`)](#including-mixin)
    *   [Mixins with Arguments](#mixin-arguments)
    *   [Passing Content to a Mixin (`@content`)](#mixin-content)
8.  [Extend/Inheritance (`@extend`)](#extend-scss)
    *   [How `@extend` Works](#how-extend-works)
    *   [Placeholder Selectors (`%`)](#placeholder-selectors)
    *   [`@extend` vs. Mixins](#extend-vs-mixins)
9.  [Operators](#operators-scss)
    *   [Mathematical Operators (`+`, `-`, `*`, `/`, `%`)](#math-operators)
    *   [Comparison Operators (`==`, `!=`, `<`, `>`, `<=`, `>=`)](#comparison-operators)
    *   [Logical Operators (`and`, `or`, `not`)](#logical-operators-scss)
    *   [String Concatenation (`+`)](#string-concatenation)
10. [Control Directives & Expressions](#control-directives-scss)
    *   [`@if`, `@else if`, `@else`](#if-else-scss)
    *   [`@for`](#for-loop-scss)
    *   [`@each`](#each-loop-scss)
    *   [`@while`](#while-loop-scss)
11. [Functions](#functions-scss)
    *   [Built-in Functions (Color, String, Number, List, Map, etc.)](#built-in-functions)
    *   [Custom Functions (`@function`)](#custom-functions)
12. [Maps](#maps-scss)
13. [Comments in SCSS](#comments-scss-types)
    *   [Loud Comments (`/* ... */`)](#loud-comments)
    *   [Silent Comments (`// ...`)](#silent-comments)
14. [Key Takeaways](#key-takeaways-scss)

## 1. Why Use a CSS Preprocessor? <a name="why-use-preprocessor"></a>
*   **DRY (Don't Repeat Yourself) Code**: Variables, mixins, and extends help reduce repetition.
*   **Improved Organization**: Partials allow you to break down large stylesheets into smaller, more manageable files.
*   **Maintainability**: Changes to common values (like brand colors) can be made in one place (variables).
*   **Readability**: Nesting can make your CSS structure mirror your HTML structure, improving readability (if not overused).
*   **Advanced Features**: Use logic like loops, conditionals, and functions to generate CSS dynamically.
*   **Easier Theming**: Variables make it simple to create different themes for a website.

## 2. Setting Up Sass/SCSS <a name="setting-up-sass"></a>
To use Sass/SCSS, you need to compile your `.scss` or `.sass` files into standard `.css` files that browsers can read.

### Using Node.js (npm) <a name="setup-npm"></a>
This is a common method. First, ensure you have Node.js and npm installed.

1.  **Install Sass globally (or locally per project):**
    ```bash
    npm install -g sass  # Global installation
    # or
    npm install --save-dev sass # Local project installation
    ```

### Command Line Compilation <a name="cli-compilation"></a>
Once Sass is installed, you can compile your files from the command line:

*   **Compile a single file:**
    ```bash
    sass input.scss output.css
    ```
*   **Watch a file for changes and recompile automatically:**
    ```bash
    sass --watch input.scss output.css
    # or for a directory:
    sass --watch scss-folder:css-folder
    ```
*   **Choose an output style (compression):**
    *   `expanded` (default): Easy to read, standard CSS.
    *   `compressed`: Minified, smallest file size for production.
    ```bash
    sass input.scss output.css --style=compressed
    ```

### GUI Applications <a name="gui-apps"></a>
There are several GUI applications that can compile Sass/SCSS for you, such as Koala, Prepros, or CodeKit (macOS).

### Build Tool Integration <a name="build-tools"></a>
For larger projects, Sass is often integrated into build tools like Webpack (with `sass-loader`), Gulp (with `gulp-sass`), or Parcel (which supports Sass out-of-the-box).

## 3. SCSS Syntax Basics <a name="scss-syntax-basics"></a>
SCSS syntax is very similar to CSS. It uses curly braces `{}` to define blocks and semicolons `;` to separate rules.

```scss
// This is valid SCSS (and also valid CSS)
body {
  font-family: Arial, sans-serif;
  line-height: 1.6;
}

.container {
  width: 90%;
  max-width: 1200px;
  margin: 0 auto;
}
```

## 4. Variables <a name="variables-scss"></a>
Variables allow you to store and reuse values like colors, font stacks, or measurements.

### Declaring Variables (`$`) <a name="declaring-variables"></a>
Variables in SCSS start with a dollar sign `$`.

```scss
$primary-color: #3498db;
$secondary-color: #2ecc71;
$base-font-size: 16px;
$base-font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;

body {
  font-family: $base-font-family;
  font-size: $base-font-size;
  color: darken($primary-color, 10%); // Using a function with a variable
}

.button-primary {
  background-color: $primary-color;
  color: white;
}
```

### Variable Scope <a name="variable-scope"></a>
Variables are typically scoped to the block where they are defined. Variables defined outside any block are global.

```scss
$global-var: #000;

.block {
  $local-var: #fff;
  color: $local-var; // Can access local-var
  background-color: $global-var; // Can access global-var
}

// color: $local-var; // Error: $local-var is not defined globally
```

### Variable Defaults (`!default`) <a name="variable-defaults"></a>
You can assign a default value to a variable if it's not already defined or is `null`. This is useful for creating configurable libraries or frameworks.

```scss
$theme-color: #f00; // User might define this before your library

// In your library:
$theme-color: #3498db !default; // Will be #3498db only if $theme-color wasn't already set to #f00

body {
  background-color: $theme-color;
}
```

## 5. Nesting <a name="nesting-scss"></a>
Nesting allows you to write CSS selectors in a way that mirrors your HTML structure, reducing repetition.

### Nesting Selectors <a name="nesting-selectors"></a>
```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;

    li {
      display: inline-block;

      a {
        display: block;
        padding: 6px 12px;
        text-decoration: none;

        &:hover { // Using parent selector for pseudo-class
          background-color: #eee;
        }
      }
    }
  }
}
```
Compiled CSS:
```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav ul li {
  display: inline-block;
}
nav ul li a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
nav ul li a:hover {
  background-color: #eee;
}
```

### Parent Selector (`&`) <a name="parent-selector"></a>
The ampersand `&` refers to the parent selector. It's essential for pseudo-classes, pseudo-elements, or modifying the parent itself (like in BEM).

```scss
.button {
  padding: 10px 15px;
  background-color: blue;
  color: white;

  &:hover {
    background-color: darken(blue, 10%);
  }

  &::before {
    content: '★';
    margin-right: 5px;
  }

  &--disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  .icon-& { // Places .icon- before .button -> .icon-button
    font-style: italic;
  }
}
```

### Nesting Properties <a name="nesting-properties"></a>
Properties with common prefixes like `font-`, `margin-`, `padding-`, `border-` can also be nested.

```scss
.element {
  font: {
    family: Arial, sans-serif;
    size: 1.2rem;
    weight: bold;
  }
  border: 1px solid #ccc {
    left: none;
    radius: 5px;
  }
}
```
Compiled CSS:
```css
.element {
  font-family: Arial, sans-serif;
  font-size: 1.2rem;
  font-weight: bold;
  border: 1px solid #ccc;
  border-left: none;
  border-radius: 5px;
}
```

### Caution with Over-Nesting <a name="caution-nesting"></a>
While nesting is useful, over-nesting (e.g., more than 3-4 levels deep) can lead to:
*   Highly specific selectors that are hard to override.
*   Bloated CSS output.
*   Reduced readability if taken too far.

Strive for a balance. BEM with minimal nesting is often a good approach.

## 6. Partials and Imports <a name="partials-imports-scss"></a>

### Partials (`_filename.scss`) <a name="partials-scss"></a>
Partials are SCSS files whose names begin with an underscore (e.g., `_variables.scss`, `_buttons.scss`). The underscore tells Sass not to compile these files into separate CSS files. They are meant to be imported into other SCSS files.

### `@import` Directive <a name="import-directive-scss"></a>
Sass extends CSS's `@import` rule. It allows you to import SCSS or Sass files (partials).
When importing a partial, you don't need to include the underscore or the file extension.

```scss
// _variables.scss
$primary-color: #333;

// _reset.scss
html, body {
  margin: 0;
  padding: 0;
}

// main.scss
@import 'variables'; // Imports _variables.scss
@import 'reset';     // Imports _reset.scss

body {
  font-family: sans-serif;
  color: $primary-color;
}
```
Unlike CSS `@import`, Sass `@import` includes the file content directly during compilation, so it doesn't cause extra HTTP requests.

## 7. Mixins <a name="mixins-scss"></a>
Mixins allow you to define reusable groups of CSS declarations that you can include throughout your stylesheet. They are similar to functions in programming languages.

### Defining a Mixin (`@mixin`) <a name="defining-mixin"></a>
```scss
@mixin reset-list {
  margin: 0;
  padding: 0;
  list-style: none;
}

@mixin fancy-border {
  border: 2px dashed purple;
  border-radius: 10px;
}
```

### Including a Mixin (`@include`) <a name="including-mixin"></a>
```scss
nav ul {
  @include reset-list;
}

.special-box {
  @include fancy-border;
  padding: 20px;
}
```
Compiled CSS:
```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}

.special-box {
  border: 2px dashed purple;
  border-radius: 10px;
  padding: 20px;
}
```

### Mixins with Arguments <a name="mixin-arguments"></a>
Mixins can accept arguments, making them more flexible. You can also provide default values for arguments.

```scss
@mixin border-radius($radius: 5px) {
  -webkit-border-radius: $radius;
  -moz-border-radius: $radius;
  border-radius: $radius;
}

@mixin transition($property: all, $duration: 0.3s, $timing: ease) {
  transition: $property $duration $timing;
}

.box {
  @include border-radius(10px);
}

.button {
  @include border-radius; // Uses default 5px
  @include transition(background-color, 0.5s, ease-in-out);
}
```

### Passing Content to a Mixin (`@content`) <a name="mixin-content"></a>
A mixin can also take a block of styles, passed using the `@content` directive. This is powerful for creating mixins that wrap content, like media query helpers.

```scss
@mixin respond-to($breakpoint) {
  @if $breakpoint == tablet {
    @media (min-width: 768px) {
      @content;
    }
  } @else if $breakpoint == desktop {
    @media (min-width: 1024px) {
      @content;
    }
  }
}

.sidebar {
  width: 100%;
  @include respond-to(tablet) {
    width: 30%;
    float: left;
  }
}
```

## 8. Extend/Inheritance (`@extend`) <a name="extend-scss"></a>
`@extend` allows selectors to inherit the styles of another selector. It helps keep your CSS DRY by grouping selectors that share common styles.

### How `@extend` Works <a name="how-extend-works"></a>
When one class extends another, the extending class is added to the list of selectors for the extended class's style block.

```scss
.message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  @extend .message;
  border-color: green;
}

.error {
  @extend .message;
  border-color: red;
}

.warning {
  @extend .message;
  border-color: orange;
}
```
Compiled CSS:
```css
.message, .success, .error, .warning {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  border-color: green;
}

.error {
  border-color: red;
}

.warning {
  border-color: orange;
}
```

### Placeholder Selectors (`%`) <a name="placeholder-selectors"></a>
Placeholder selectors (e.g., `%placeholder-name`) are like classes but start with `%`. They are useful with `@extend` because they only print to CSS if they are extended. If a placeholder selector is never extended, it won't appear in the compiled CSS.

```scss
%base-button-style {
  padding: 10px 15px;
  font-size: 1rem;
  cursor: pointer;
}

.button-primary {
  @extend %base-button-style;
  background-color: blue;
  color: white;
}

.button-secondary {
  @extend %base-button-style;
  background-color: grey;
  color: black;
}
```
Compiled CSS:
```css
.button-primary, .button-secondary {
  padding: 10px 15px;
  font-size: 1rem;
  cursor: pointer;
}

.button-primary {
  background-color: blue;
  color: white;
}

.button-secondary {
  background-color: grey;
  color: black;
}
```

### `@extend` vs. Mixins <a name="extend-vs-mixins"></a>
*   **`@extend`**: Best for sharing semantic relationships between selectors. It groups selectors in the output, leading to more compact CSS but potentially more complex selector lists.
*   **`@mixin`**: Best for including a bundle of properties, especially when arguments are needed for variation. Mixins duplicate properties for each inclusion, which can lead to larger CSS if overused for simple property sharing.

Generally, if there's a clear "is-a" relationship (e.g., a `.success-message` *is a type of* `.message`), `@extend` might be appropriate. If you need to pass parameters or just bundle unrelated properties, use a `@mixin`.

## 9. Operators <a name="operators-scss"></a>
Sass supports standard mathematical operators, as well as some specific to its data types.

### Mathematical Operators (`+`, `-`, `*`, `/`, `%`) <a name="math-operators"></a>
Can be used with numbers, often with units. Sass handles unit conversions.

```scss
.element {
  width: 100px + 50px;       // 150px
  font-size: 10px * 2;       // 20px
  margin-left: (20px / 2);   // 10px (parentheses needed for / in some contexts)
  padding: (10em + 5rem) / 2; // Sass tries to manage units
}

$base-spacing: 10px;
.container {
  padding: $base-spacing;
  margin-bottom: $base-spacing * 2;
}
```
Note: `/` for division can conflict with the CSS shorthand separator. Wrap division operations in parentheses `()` or use the `math.div()` module function (in Dart Sass).

### Comparison Operators (`==`, `!=`, `<`, `>`, `<=`, `>=`) <a name="comparison-operators"></a>
Used in control directives like `@if`.

### Logical Operators (`and`, `or`, `not`) <a name="logical-operators-scss"></a>
Used in control directives.

### String Concatenation (`+`) <a name="string-concatenation"></a>
Strings can be concatenated using `+`.
```scss
$base-url: '/images/';
.icon {
  background-image: url($base-url + 'icon.png'); // url('/images/icon.png')
}
```

## 10. Control Directives & Expressions <a name="control-directives-scss"></a>
Sass allows for conditional logic and loops.

### `@if`, `@else if`, `@else` <a name="if-else-scss"></a>
Conditionally include styles.

```scss
@mixin set-text-color($theme) {
  @if $theme == dark {
    color: white;
  } @else if $theme == light {
    color: black;
  } @else {
    color: grey; // Default
  }
}

.dark-mode {
  @include set-text-color(dark);
}
```

### `@for` <a name="for-loop-scss"></a>
Outputs styles in a loop. Two forms: `through` (inclusive) and `to` (exclusive).

```scss
// Creates .col-1, .col-2, ..., .col-12
@for $i from 1 through 12 {
  .col-#{$i} {
    width: (100% / 12) * $i;
  }
}
```
`#{$variable}` is variable interpolation, used to insert variable values into selectors or property names.

### `@each` <a name="each-loop-scss"></a>
Iterates over items in a list or keys/values in a map.

```scss
$colors: (primary: blue, secondary: green, danger: red);

@each $name, $color in $colors {
  .button-#{$name} {
    background-color: $color;
  }
}

$icons: 'user', 'cart', 'search';
@each $icon in $icons {
  .icon-#{$icon}::before {
    content: url('/icons/#{$icon}.svg');
  }
}
```

### `@while` <a name="while-loop-scss"></a>
Repeats styles as long as a condition is true.

```scss
$i: 6;
@while $i > 0 {
  .item-#{$i} {
    width: 10px * $i;
  }
  $i: $i - 2;
}
```

## 11. Functions <a name="functions-scss"></a>
Sass has many built-in functions and allows you to define your own.

### Built-in Functions <a name="built-in-functions"></a>
Sass provides functions for manipulating:
*   **Colors**: `lighten()`, `darken()`, `saturate()`, `desaturate()`, `adjust-hue()`, `rgba()`, `mix()`.
*   **Strings**: `unquote()`, `quote()`, `str-length()`, `to-upper-case()`.
*   **Numbers**: `percentage()`, `round()`, `ceil()`, `floor()`, `abs()`, `min()`, `max()`.
*   **Lists**: `length()`, `nth()`, `join()`, `append()`.
*   **Maps**: `map-get()`, `map-has-key()`, `map-keys()`, `map-values()`.
*   **Selectors**: `selector-nest()`, `selector-append()`.
*   **Introspection**: `type-of()`, `unit()`, `unitless()`.

```scss
$base-color: #ff0000; // Red

.element {
  color: $base-color;
  background-color: lighten($base-color, 20%); // Lighter red
  border-color: darken($base-color, 10%);   // Darker red
}
```

### Custom Functions (`@function`) <a name="custom-functions"></a>
You can define your own functions to encapsulate complex logic or calculations. Functions must use `@return` to output a value.

```scss
@function calculate-em($target-pixels, $context-pixels: 16px) {
  @return ($target-pixels / $context-pixels) * 1em;
}

.title {
  font-size: calculate-em(24px);
}
```

## 12. Maps <a name="maps-scss"></a>
Maps are collections of key-value pairs, similar to objects in JavaScript or dictionaries in Python. They are great for storing structured data like breakpoints, color palettes, or font stacks.

```scss
$breakpoints: (
  'small': 320px,
  'medium': 768px,
  'large': 1024px
);

$colors: (
  'primary': blue,
  'accent': green,
  'warning': orange
);

.container {
  width: 90%;
  @media (min-width: map-get($breakpoints, 'medium')) {
    width: 80%;
  }
}

.button-warning {
  background-color: map-get($colors, 'warning');
}
```

## 13. Comments in SCSS <a name="comments-scss-types"></a>

### Loud Comments (`/* ... */`) <a name="loud-comments"></a>
These are standard CSS comments. They are preserved in the compiled CSS output.
```scss
/* This is a loud comment and will appear in the CSS file. */
.element {
  color: black;
}
```

### Silent Comments (`// ...`) <a name="silent-comments"></a>
These comments are only visible in the SCSS file and are stripped out during compilation. They will not appear in the final CSS output. Ideal for notes to developers.
```scss
// This is a silent comment and will NOT appear in the CSS file.
$primary-color: blue; // Define primary color for the theme
```

## 14. Key Takeaways <a name="key-takeaways-scss"></a>
*   Sass/SCSS enhances CSS with powerful features like variables, nesting, mixins, extend, and logic.
*   SCSS syntax is a superset of CSS, making it easy to adopt.
*   Use variables for reusable values and theming.
*   Use nesting judiciously to improve readability without creating overly specific selectors.
*   Organize code with partials (`_filename.scss`) and `@import`.
*   Leverage mixins for reusable blocks of styles, especially with arguments.
*   Use `@extend` for sharing semantic styles and reducing code duplication via selector grouping.
*   Placeholder selectors (`%`) are great for `@extend`-only base styles.
*   Control directives (`@if`, `@for`, `@each`) allow for dynamic CSS generation.
*   Sass provides many useful built-in functions and allows custom function creation.
*   Maps are useful for storing structured data.
*   Always compile your `.scss` files to `.css` for browser use.

Sass/SCSS can significantly improve your CSS workflow, making your stylesheets more robust, maintainable, and easier to scale.

---

⬅️ [Back to CSS Best Practices](../14-css-best-practices/README.md) | ➡️ [Next Section: CSS Frameworks (Bootstrap/Tailwind)](../16-css-frameworks/README.md)
