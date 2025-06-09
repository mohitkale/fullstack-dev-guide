# 6. HTML Forms

HTML forms are a fundamental part of web interaction, allowing users to input data that can then be submitted to a web server for processing. This section covers the various elements and attributes used to create robust and user-friendly forms.

## Table of Contents
1. [The `<form>` Element](#form-element)
   - [`action` Attribute](#action-attribute)
   - [`method` Attribute (GET vs. POST)](#method-attribute)
   - [`name` Attribute](#name-attribute-form)
   - [`autocomplete` Attribute](#autocomplete-attribute-form)
   - [`novalidate` Attribute](#novalidate-attribute)
2. [Basic Input Fields (`<input>`)](#input-element)
   - [`type` Attribute (Common Values)](#type-attribute-input)
     - `text`, `password`, `email`, `number`, `date`, `url`, `tel`, `search`
   - [`name` Attribute (for Inputs)](#name-attribute-input)
   - [`value` Attribute](#value-attribute-input)
   - [`placeholder` Attribute](#placeholder-attribute)
   - [`required` Attribute](#required-attribute)
   - [`readonly` Attribute](#readonly-attribute)
   - [`disabled` Attribute](#disabled-attribute)
   - [`size` and `maxlength` Attributes (for text inputs)](#size-maxlength-attributes)
3. [Labels (`<label>`)](#label-element)
   - [Associating Labels with Inputs (`for` and `id`)](#associating-labels)
4. [Buttons](#buttons)
   - [`<input type="submit">`](#input-submit)
   - [`<input type="reset">`](#input-reset)
   - [`<input type="button">`](#input-button)
   - [`<button>` Element](#button-element)
5. [Checkboxes (`<input type="checkbox">`)](#checkboxes)
6. [Radio Buttons (`<input type="radio">`)](#radio-buttons)
7. [Text Areas (`<textarea>`)](#textarea-element)
   - [`rows` and `cols` Attributes](#rows-cols-attributes)
8. [Select Dropdowns (`<select>`, `<option>`, `<optgroup>`)](#select-dropdowns)
   - [`<select>` Element](#select-element)
   - [`<option>` Element](#option-element)
   - [`<optgroup>` Element](#optgroup-element)
   - [`value` Attribute for Options](#value-attribute-option)
   - [`selected` Attribute for Options](#selected-attribute-option)
   - [`multiple` Attribute for Select](#multiple-attribute-select)
9. [File Uploads (`<input type="file">`)](#file-uploads)
10. [Hidden Fields (`<input type="hidden">`)](#hidden-fields)
11. [Fieldsets and Legends (`<fieldset>`, `<legend>`)](#fieldset-legend)
12. [HTML5 Input Types (More Examples)](#html5-input-types)
    - `color`, `range`, `time`, `datetime-local`, `month`, `week`
13. [Form Validation (Client-Side Basics)](#form-validation)
14. [Best Practices for Forms](#best-practices-for-forms)
15. [Key Takeaways](#key-takeaways)

## The `<form>` Element <a name="form-element"></a>

The `<form>` element is a container for different types of input elements, such as text fields, checkboxes, radio buttons, submit buttons, etc. It defines a section of the document that collects user input.

```html
<form action="/submit-your-data" method="post">
    <!-- Form elements go here -->
</form>
```

### `action` Attribute <a name="action-attribute"></a>
*   The `action` attribute specifies the URL where the form data should be sent when the form is submitted.
*   If omitted, the data is typically submitted to the current page's URL.

### `method` Attribute (GET vs. POST) <a name="method-attribute"></a>
*   The `method` attribute specifies the HTTP method to be used when submitting the form data.
    *   **`GET`**: 
        *   Appends form data to the URL in name/value pairs (e.g., `url?name1=value1&name2=value2`).
        *   Suitable for short, non-sensitive data (like search queries).
        *   Has limitations on data length.
        *   Submissions can be bookmarked and cached.
        *   **Never use GET for sensitive data (passwords, credit card info).**
    *   **`POST`**: 
        *   Sends form data in the HTTP request body.
        *   Suitable for larger amounts of data and sensitive information.
        *   No limitations on data length (within server limits).
        *   Submissions are not bookmarked or cached by default.
        *   Generally preferred for forms that modify data on the server.

### `name` Attribute (for Forms) <a name="name-attribute-form"></a>
*   The `name` attribute gives the form a name. While less common for the `<form>` element itself in modern usage (often identified by `id` for JavaScript), it can be useful if you need to reference the form specifically, especially with multiple forms on a page or legacy JavaScript.

### `autocomplete` Attribute (for Forms) <a name="autocomplete-attribute-form"></a>
*   The `autocomplete` attribute specifies whether a form should have autocomplete on or off. When `autocomplete` is on, the browser automatically completes values based on values that the user has entered before.
    *   `on` (default): Browser can autocomplete.
    *   `off`: Browser should not autocomplete.
    ```html
    <form action="/login" method="post" autocomplete="on">
        <!-- ... -->
    </form>
    ```

### `novalidate` Attribute <a name="novalidate-attribute"></a>
*   The `novalidate` attribute is a boolean attribute. When present, it specifies that the form-data (input) should not be validated when submitted. This bypasses HTML5's built-in client-side validation.
    ```html
    <form action="/submit" method="post" novalidate>
        <!-- ... -->
    </form>
    ```

## Basic Input Fields (`<input>`) <a name="input-element"></a>

The `<input>` element is the most versatile form element. Its behavior is determined by its `type` attribute.

### `type` Attribute (Common Values) <a name="type-attribute-input"></a>
*   `type="text"`: A single-line text input field.
    ```html
    <input type="text" name="username">
    ```
*   `type="password"`: A single-line text input field where characters are masked (e.g., with asterisks or dots).
    ```html
    <input type="password" name="pwd">
    ```
*   `type="email"`: For email addresses. Browsers may provide validation for email format.
    ```html
    <input type="email" name="useremail">
    ```
*   `type="number"`: For numerical input. Browsers may provide spinner controls.
    ```html
    <input type="number" name="quantity" min="1" max="10">
    ```
*   `type="date"`: For selecting a date. Browsers may provide a date picker.
    ```html
    <input type="date" name="birthdate">
    ```
*   `type="url"`: For URLs. Browsers may provide validation.
*   `type="tel"`: For telephone numbers. Behavior varies by browser/device.
*   `type="search"`: For search fields. May be styled differently by browsers.

### `name` Attribute (for Inputs) <a name="name-attribute-input"></a>
*   The `name` attribute is crucial for input fields. When the form is submitted, the `name` of each input field is sent along with its `value` to the server. Without a `name`, the data from that input field will not be submitted.

### `value` Attribute <a name="value-attribute-input"></a>
*   The `value` attribute specifies the initial value of an input field. For `checkbox` and `radio` types, it specifies the value to be sent if selected.
    ```html
    <input type="text" name="firstname" value="John">
    ```

### `placeholder` Attribute <a name="placeholder-attribute"></a>
*   The `placeholder` attribute provides a short hint or example text displayed in the input field before the user enters a value. It disappears when the user starts typing.
    ```html
    <input type="text" name="email" placeholder="you@example.com">
    ```

### `required` Attribute <a name="required-attribute"></a>
*   A boolean attribute indicating that the input field must be filled out before submitting the form.
    ```html
    <input type="text" name="username" required>
    ```

### `readonly` Attribute <a name="readonly-attribute"></a>
*   A boolean attribute indicating that the input field cannot be modified by the user. Its value will still be submitted with the form.
    ```html
    <input type="text" name="user_id" value="12345" readonly>
    ```

### `disabled` Attribute <a name="disabled-attribute"></a>
*   A boolean attribute indicating that the input field is unusable and un-clickable. Disabled fields are not submitted with the form.
    ```html
    <input type="text" name="coupon_code" value="EXPIRED" disabled>
    ```

### `size` and `maxlength` Attributes (for text inputs) <a name="size-maxlength-attributes"></a>
*   `size`: Specifies the visible width of the input field, in characters (for `text`, `password`, `email`, etc.).
*   `maxlength`: Specifies the maximum number of characters allowed in the input field.
    ```html
    <input type="text" name="zipcode" size="5" maxlength="5">
    ```

## Labels (`<label>`) <a name="label-element"></a>

The `<label>` element defines a label for several form elements (`<input>`, `<textarea>`, `<select>`, etc.).

### Associating Labels with Inputs (`for` and `id`) <a name="associating-labels"></a>
*   Properly associating labels with their form controls is crucial for accessibility:
    1.  Give the input element an `id` attribute.
    2.  Use the `for` attribute on the `<label>` element, with its value matching the input's `id`.
*   This allows users to click on the label to focus on or activate the associated input field. Screen readers also use this association.

```html
<label for="username">Username:</label>
<input type="text" id="username" name="username">

<!-- Alternatively, you can wrap the input inside the label (implicit association): -->
<label>Email: <input type="email" name="useremail"></label>
<!-- Explicit association with for/id is generally preferred for clarity and robustness. -->
```

## Buttons <a name="buttons"></a>

### `<input type="submit">` <a name="input-submit"></a>
*   Defines a button that, when clicked, submits the form data to the URL specified in the form's `action` attribute.
*   The text on the button can be set using the `value` attribute.
    ```html
    <input type="submit" value="Register">
    ```

### `<input type="reset">` <a name="input-reset"></a>
*   Defines a button that resets all form controls to their initial values.
    ```html
    <input type="reset" value="Clear Form">
    ```

### `<input type="button">` <a name="input-button"></a>
*   Defines a generic clickable button. Its behavior is typically defined using JavaScript.
    ```html
    <input type="button" value="Click Me" onclick="alert('Hello!');">
    ```

### `<button>` Element <a name="button-element"></a>
*   The `<button>` element is more flexible than `<input type="button">`.
*   It can contain HTML content (like text, images, or other elements), offering richer styling possibilities.
*   Its `type` attribute can be `submit` (default), `reset`, or `button`.

```html
<button type="submit">Send Data</button>
<button type="reset">Reset Fields</button>
<button type="button" onclick="doSomething();">
    <img src="icon.png" alt=""> Custom Action
</button>
```
**Recommendation**: Prefer `<button>` over `<input type="submit">`, `<input type="reset">`, and `<input type="button">` for more flexibility in content and styling.

## Checkboxes (`<input type="checkbox">`) <a name="checkboxes"></a>
*   Checkboxes allow users to select zero, one, or multiple options from a set.
*   Each checkbox should have a unique `name` if they represent different choices, or the same `name` (often with `[]` in PHP, e.g., `name="interests[]"`) if they represent multiple selections for a single category.
*   The `value` attribute specifies the data to be sent if the checkbox is checked.
*   The `checked` boolean attribute pre-selects a checkbox.

```html
<input type="checkbox" id="vehicle1" name="vehicle1" value="Bike">
<label for="vehicle1"> I have a bike</label><br>
<input type="checkbox" id="vehicle2" name="vehicle2" value="Car" checked>
<label for="vehicle2"> I have a car</label><br>
```

## Radio Buttons (`<input type="radio">`) <a name="radio-buttons"></a>
*   Radio buttons allow users to select only one option from a limited set of mutually exclusive choices.
*   All radio buttons in a group must share the same `name` attribute.
*   Each radio button should have a unique `value` attribute.
*   The `checked` boolean attribute pre-selects a radio button.

```html
<p>Select your gender:</p>
<input type="radio" id="male" name="gender" value="male">
<label for="male">Male</label><br>
<input type="radio" id="female" name="gender" value="female" checked>
<label for="female">Female</label><br>
<input type="radio" id="other" name="gender" value="other">
<label for="other">Other</label>
```

## Text Areas (`<textarea>`) <a name="textarea-element"></a>
*   The `<textarea>` element defines a multi-line text input field.
*   Text placed between the opening `<textarea>` and closing `</textarea>` tags becomes the default content.

```html
<label for="message">Message:</label><br>
<textarea id="message" name="message" rows="5" cols="40">
Enter your message here...</textarea>
```

### `rows` and `cols` Attributes <a name="rows-cols-attributes"></a>
*   `rows`: Specifies the visible number of lines in the text area.
*   `cols`: Specifies the visible width of the text area, in average character widths.
*   Note: The actual size can also be controlled more flexibly with CSS.

## Select Dropdowns (`<select>`, `<option>`, `<optgroup>`) <a name="select-dropdowns"></a>

### `<select>` Element <a name="select-element"></a>
*   The `<select>` element creates a drop-down list.
*   The `name` attribute is assigned to the `<select>` element.

### `<option>` Element <a name="option-element"></a>
*   Each item in the drop-down list is defined by an `<option>` element nested within the `<select>`.

### `<optgroup>` Element <a name="optgroup-element"></a>
*   The `<optgroup>` element can be used to group related options within a `<select>` list. It has a `label` attribute that provides a heading for the group.

### `value` Attribute for Options <a name="value-attribute-option"></a>
*   The `value` attribute of an `<option>` tag specifies the value that will be sent to the server if that option is selected. If omitted, the text content of the option is sent.

### `selected` Attribute for Options <a name="selected-attribute-option"></a>
*   The `selected` boolean attribute pre-selects an option in the drop-down list.

### `multiple` Attribute for Select <a name="multiple-attribute-select"></a>
*   The `multiple` boolean attribute on `<select>` allows the user to select more than one option (usually by holding Ctrl/Cmd or Shift while clicking).

```html
<label for="cars">Choose a car:</label>
<select id="cars" name="cars">
    <optgroup label="Swedish Cars">
        <option value="volvo">Volvo</option>
        <option value="saab" selected>Saab</option>
    </optgroup>
    <optgroup label="German Cars">
        <option value="mercedes">Mercedes</option>
        <option value="audi">Audi</option>
    </optgroup>
</select>

<label for="pets">Choose pets (multiple):</label>
<select id="pets" name="pets[]" multiple size="3">
    <option value="dog">Dog</option>
    <option value="cat">Cat</option>
    <option value="hamster">Hamster</option>
    <option value="parrot">Parrot</option>
</select>
```
(Note: `name="pets[]"` is a common convention for PHP to receive multiple selections as an array.)

## File Uploads (`<input type="file">`) <a name="file-uploads"></a>
*   Allows users to select one or more files from their device to be uploaded to the server.
*   When using file uploads, the form's `method` attribute **must be `POST`**, and the `enctype` attribute **must be set to `multipart/form-data`**.

```html
<form action="/upload-file" method="post" enctype="multipart/form-data">
    <label for="myfile">Select a file:</label>
    <input type="file" id="myfile" name="myfile"><br><br>
    <input type="submit" value="Upload">
</form>
```
*   The `accept` attribute can suggest file types (e.g., `accept="image/png, image/jpeg"`).
*   The `multiple` attribute allows selecting multiple files.

## Hidden Fields (`<input type="hidden">`) <a name="hidden-fields"></a>
*   Hidden input fields are not displayed to the user but their values are still submitted with the form.
*   They are often used to send extra data that the user doesn't need to see or interact with, such as session IDs, CSRF tokens, or tracking information.

```html
<input type="hidden" name="user_id" value="12345">
<input type="hidden" name="csrf_token" value="xyz789abc">
```

## Fieldsets and Legends (`<fieldset>`, `<legend>`) <a name="fieldset-legend"></a>
*   The `<fieldset>` element is used to group related elements in a form, drawing a box around them by default.
*   The `<legend>` element defines a caption for the `<fieldset>`.
*   This helps organize complex forms and improves accessibility by providing context for grouped controls.

```html
<form action="/submit">
    <fieldset>
        <legend>Personal Information:</legend>
        <label for="fname">First name:</label><br>
        <input type="text" id="fname" name="fname"><br>
        <label for="lname">Last name:</label><br>
        <input type="text" id="lname" name="lname">
    </fieldset>

    <fieldset>
        <legend>Contact Details:</legend>
        <label for="email">Email:</label><br>
        <input type="email" id="email" name="email">
    </fieldset>
    <input type="submit" value="Submit">
</form>
```

## HTML5 Input Types (More Examples) <a name="html5-input-types"></a>
HTML5 introduced several new input types that can provide enhanced user experiences and built-in validation on supporting browsers:
*   `type="color"`: A color picker.
*   `type="range"`: A slider control for a range of numbers.
*   `type="time"`: For selecting a time.
*   `type="datetime-local"`: For selecting a date and time (local).
*   `type="month"`: For selecting a month and year.
*   `type="week"`: For selecting a week and year.

```html
<label for="favcolor">Select your favorite color:</label>
<input type="color" id="favcolor" name="favcolor" value="#ff0000"><br>

<label for="volume">Volume:</label>
<input type="range" id="volume" name="volume" min="0" max="100" value="50"><br>

<label for="appt">Select a time:</label>
<input type="time" id="appt" name="appt"><br>
```
Browser support and appearance for these types can vary.

## Form Validation (Client-Side Basics) <a name="form-validation"></a>
HTML5 provides built-in client-side validation features:
*   **`required` attribute**: Ensures a field is filled.
*   **Type-specific validation**: `email`, `url`, `number` types automatically check for valid formats/ranges.
*   **`pattern` attribute**: Allows you to specify a regular expression for the input value to match.
    ```html
    <label for="zip">Zip Code (5 digits):</label>
    <input type="text" id="zip" name="zip" pattern="[0-9]{5}" title="Five digit zip code" required>
    ```
*   **`min` and `max` attributes**: For number and date types.
*   **`minlength` and `maxlength` attributes**: For text-based inputs.

Client-side validation provides quick feedback to users but **should always be complemented by server-side validation** because client-side validation can be bypassed.

## Best Practices for Forms <a name="best-practices-for-forms"></a>
*   **Accessibility**: Always use `<label>` elements correctly associated with form controls. Use `<fieldset>` and `<legend>` for grouping.
*   **Clarity**: Keep forms simple and logical. Only ask for necessary information.
*   **Usability**: Ensure large enough click targets, clear instructions, and helpful error messages.
*   **Security**: Use `POST` for sensitive data. Implement server-side validation and sanitization. Protect against CSRF and XSS attacks (these are more advanced server-side concerns).
*   **Mobile-Friendliness**: Design forms that work well on small screens.
*   **Feedback**: Provide clear feedback on submission (success or errors).

## Key Takeaways

*   The `<form>` element encapsulates form controls.
*   `action` specifies where to send data; `method` (GET/POST) specifies how.
*   `<input>` is versatile, with its `type` attribute defining its behavior (text, password, email, checkbox, radio, submit, etc.).
*   `<label>` is crucial for accessibility, linking text descriptions to form controls.
*   `<textarea>` for multi-line text; `<select>` for dropdowns.
*   Use `name` attributes on controls for data submission.
*   HTML5 offers new input types and built-in validation features.
*   Always combine client-side validation with robust server-side validation.

Forms are a cornerstone of interactive web applications, and understanding their structure and best practices is vital for any web developer.

---

🔙 [Back to Tables](../05-tables/README.md) | ➡️ [Next Section: Semantic HTML](../07-semantic-html/README.md)
