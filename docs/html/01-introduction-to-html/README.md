# 1. Introduction to HTML

Welcome to the first step in your HTML journey! In this section, we'll explore what HTML is, its role in web development, its basic structure, and how to create your very first HTML page.

## Table of Contents
1. [What is HTML?](#what-is-html)
2. [A Brief History of HTML](#a-brief-history-of-html)
3. [Role of HTML in Web Development](#role-of-html-in-web-development)
4. [Basic Structure of an HTML Page](#basic-structure-of-an-html-page)
   - [`<!DOCTYPE html>`](#doctype)
   - [`<html>` Element](#html-element)
   - [`<head>` Element](#head-element)
   - [`<body>` Element](#body-element)
5. [Setting Up Your First HTML File](#setting-up-your-first-html-file)
6. [Viewing Your HTML Page](#viewing-your-html-page)
7. [Key Takeaways](#key-takeaways)

## What is HTML?

HTML stands for **HyperText Markup Language**. Let's break that down:

*   **HyperText**: This refers to text that contains links (hyperlinks) to other texts or documents. When you click on a link on a webpage, you're using hypertext to navigate.
*   **Markup Language**: This means HTML uses tags to "mark up" or describe the structure and content of a web page. These tags tell the web browser how to display the different parts of the page, such as headings, paragraphs, images, and links.

Essentially, HTML is the standard language used to create and design documents that are displayed on the World Wide Web. It's the skeleton or framework of a webpage.

## A Brief History of HTML

-   **Early 1990s**: Tim Berners-Lee, a physicist at CERN, invented HTML while developing the World Wide Web.
-   **HTML 2.0 (1995)**: The first official HTML standard.
-   **HTML 3.2 (1997)**: Introduced more features like tables and applets.
-   **HTML 4.01 (1999)**: A widely adopted version that emphasized separation of structure (HTML) from presentation (CSS).
-   **XHTML 1.0 (2000)**: A stricter, XML-based version of HTML.
-   **HTML5 (2014 - Recommendation)**: The current major version. It introduced many new features, including semantic elements (like `<article>`, `<header>`), multimedia support (`<video>`, `<audio>`), and APIs for more complex web applications.

Understanding this evolution helps appreciate why certain features exist and how HTML has adapted to the growing needs of the web.

## Role of HTML in Web Development

In web development, three core technologies work together:

1.  **HTML (Structure)**: Defines the content and structure of a web page. Think of it as the nouns and organization of your document.
2.  **CSS (Cascading Style Sheets) (Presentation)**: Describes how the HTML elements should be styled and displayed (e.g., colors, fonts, layout). Think of it as the adjectives and visual design.
3.  **JavaScript (Behavior)**: Adds interactivity and dynamic behavior to web pages (e.g., animations, form validations, fetching data). Think of it as the verbs and actions.

HTML is the starting point. Without HTML, there's no content for CSS to style or JavaScript to manipulate.

## Basic Structure of an HTML Page

Every HTML document follows a fundamental structure. Here's a minimal example:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My First Web Page</title>
</head>
<body>
    <h1>Hello, World!</h1>
    <p>This is my first paragraph.</p>
</body>
</html>
```

Let's break down each part:

### `<!DOCTYPE html>` <a name="doctype"></a>
*   This is the **document type declaration**. It's not an HTML tag itself but an instruction to the web browser about what version of HTML the page is written in.
*   `<!DOCTYPE html>` specifically tells the browser that the page is using HTML5, which is the modern standard. It ensures the browser renders the page in "standards mode," leading to more consistent behavior across different browsers.
*   It should always be the very first line in your HTML document.

### `<html>` Element <a name="html-element"></a>
*   This is the **root element** of an HTML page.
*   All other elements (except `<!DOCTYPE>`) must be descendants of this element.
*   The `lang` attribute (e.g., `lang="en"`) specifies the language of the document content, which is important for accessibility and search engines. `en` stands for English.

### `<head>` Element <a name="head-element"></a>
*   The `<head>` element contains **meta-information** about the HTML document. This information is not displayed directly on the web page itself (except for the `<title>`).
*   Common elements found in the `<head>` include:
    *   `<meta charset="UTF-8">`: Specifies the character encoding for the document. UTF-8 is a universal character set that supports almost all characters and symbols from all languages.
    *   `<meta name="viewport" content="width=device-width, initial-scale=1.0">`: Configures the viewport for responsive web design. It tells the browser to set the width of the page to the device's screen width and set the initial zoom level to 1.
    *   `<title>My First Web Page</title>`: Defines the title of the document. This title is displayed in the browser's title bar or tab, and is also used by search engines.
    *   Links to CSS files (`<link>`), JavaScript files (`<script>`), and other metadata.

### `<body>` Element <a name="body-element"></a>
*   The `<body>` element contains all the **visible content** of the HTML document that is displayed to the user in the browser window.
*   This includes headings, paragraphs, images, links, tables, lists, and everything else the user sees and interacts with.
*   In our example, `<h1>Hello, World!</h1>` (a top-level heading) and `<p>This is my first paragraph.</p>` (a paragraph) are inside the `<body>`.

## Setting Up Your First HTML File

Creating an HTML file is simple:

1.  **Open a Text Editor**: You can use any plain text editor. Some popular choices for coding include:
    *   Visual Studio Code (VS Code) - Highly recommended, free, and feature-rich.
    *   Sublime Text
    *   Atom
    *   Notepad++ (Windows)
    *   TextEdit (Mac - make sure it's in plain text mode)
    Avoid using word processors like Microsoft Word or Google Docs, as they add extra formatting that will break your HTML.

2.  **Write Your HTML Code**: Type or copy the basic HTML structure example from above into your text editor.

3.  **Save the File**: 
    *   Save the file with an `.html` or `.htm` extension. For example, `index.html` or `my_first_page.html`.
    *   `index.html` is a common name for the main page or homepage of a website.
    *   When saving, ensure the "Save as type" or "Format" is set to "All Files" or "Plain Text" if your editor defaults to something like `.txt`.

**Example File: `index.html`**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Awesome Page</title>
</head>
<body>
    <h1>Welcome to My Page!</h1>
    <p>I'm learning HTML, and it's fun!</p>
</body>
</html>
```

## Viewing Your HTML Page

Once you've saved your HTML file:

1.  **Navigate to the File**: Open your computer's file explorer (Finder on Mac, File Explorer on Windows) and find where you saved the `.html` file.
2.  **Open in a Web Browser**: Double-click the file, or right-click and choose "Open with" your preferred web browser (e.g., Google Chrome, Mozilla Firefox, Safari, Microsoft Edge).

The browser will read your HTML code and display the content as a web page!

## Key Takeaways

*   HTML provides the **structure** for web pages.
*   The `<!DOCTYPE html>` declaration is crucial for modern HTML5.
*   An HTML document is built with `<html>`, `<head>`, and `<body>` elements.
*   The `<head>` contains metadata (like title and character set), not visible content.
*   The `<body>` contains all the visible content of the page.
*   HTML files are plain text files saved with an `.html` or `.htm` extension.

Congratulations! You now understand the fundamental building blocks of an HTML page and how to create one. In the next section, we'll dive deeper into basic HTML elements.

---

🔙 [Back to HTML Overview](../README.md) | ➡️ [Next Section: Basic HTML Elements](../02-basic-html-elements/README.md)
