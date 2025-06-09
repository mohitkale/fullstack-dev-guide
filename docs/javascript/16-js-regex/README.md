# 16. JavaScript Regular Expressions (RegExp)

Regular Expressions (often shortened to RegExp or Regex) are powerful patterns used to match character combinations in strings. In JavaScript, regular expressions are also objects. They are used for a wide variety of tasks, including data validation, searching, and replacing text.

## Table of Contents
1.  [What are Regular Expressions?](#what-are-regex)
2.  [Creating Regular Expressions](#creating-regex)
    *   [Regular Expression Literal](#regex-literal)
    *   [RegExp Constructor](#regex-constructor)
3.  [RegExp Syntax (Patterns)](#regex-syntax)
    *   [Simple Patterns (Literal Characters)](#simple-patterns)
    *   [Special Characters (Metacharacters)](#metacharacters)
        *   `.` (Wildcard)
        *   `\` (Escape Character)
    *   [Character Classes](#character-classes)
        *   `[...]` (Set of characters)
        *   `[^...]` (Negated set)
        *   Ranges (e.g., `[a-z]`, `[0-9]`)
        *   Predefined Character Classes (`\d`, `\D`, `\w`, `\W`, `\s`, `\S`)
    *   [Quantifiers](#quantifiers)
        *   `*` (Zero or more)
        *   `+` (One or more)
        *   `?` (Zero or one, or makes quantifier non-greedy)
        *   `{n}` (Exactly n times)
        *   `{n,}` (At least n times)
        *   `{n,m}` (Between n and m times)
        *   Greedy vs. Non-Greedy (Lazy) Quantifiers
    *   [Anchors and Boundaries](#anchors-boundaries)
        *   `^` (Start of string/line)
        *   `$` (End of string/line)
        *   `\b` (Word boundary)
        *   `\B` (Non-word boundary)
    *   [Grouping and Capturing](#grouping-capturing)
        *   `(...)` (Capturing group)
        *   `(?:...)` (Non-capturing group)
        *   Backreferences (`\1`, `\2`, etc.)
    *   [Alternation (`|`)](#alternation)
    *   [Lookahead and Lookbehind (Advanced)](#lookaround)
        *   `(?=...)` (Positive lookahead)
        *   `(?!...)` (Negative lookahead)
        *   `(?<=...)` (Positive lookbehind)
        *   `(?<!...)` (Negative lookbehind)
4.  [Regular Expression Flags](#regex-flags)
    *   `g` (Global search)
    *   `i` (Case-insensitive search)
    *   `m` (Multiline mode)
    *   `u` (Unicode mode)
    *   `s` (`dotAll` mode - `.` matches newline characters)
    *   `y` (Sticky search)
5.  [Using Regular Expressions in JavaScript](#using-regex-js)
    *   [`RegExp` Object Methods](#regexp-object-methods)
        *   `test()`
        *   `exec()`
    *   `String` Object Methods](#string-object-methods)
        *   `match()`
        *   `matchAll()` (ES2020)
        *   `search()`
        *   `replace()`
        *   `split()`
6.  [Common Use Cases](#regex-common-uses)
    *   [Validating Input (e.g., email, phone number)](#regex-validation)
    *   [Searching for Patterns](#regex-searching)
    *   [Replacing Text](#regex-replacing)
    *   [Extracting Data](#regex-extracting)
7.  [Best Practices and Pitfalls](#regex-best-practices)
8.  [Key Takeaways](#key-takeaways-regex)

## 1. What are Regular Expressions? <a name="what-are-regex"></a>
A regular expression is a sequence of characters that defines a search pattern. This pattern can be used to check if a string contains a specific sequence of characters, or to find and replace character sequences within a string.

## 2. Creating Regular Expressions <a name="creating-regex"></a>
In JavaScript, regular expressions can be created in two ways:

### Regular Expression Literal <a name="regex-literal"></a>
This is the most common way. The pattern is enclosed between two forward slashes `/`, followed by optional flags.
```javascript
const regexLiteral = /abc/g; // Matches 'abc' globally
const emailPattern = /^[\w.-]+@[\w.-]+\.[a-zA-Z]{2,}$/i; // Simple email pattern, case-insensitive
```
Literals are compiled when the script is loaded. Use this when the regex pattern is constant.

### `RegExp` Constructor <a name="regex-constructor"></a>
You can also create a RegExp object by using the `RegExp()` constructor. This is useful when the pattern is dynamic or comes from user input (though be careful with user input to avoid ReDoS - Regular Expression Denial of Service).
```javascript
const patternString = "abc";
const flags = "g";
const regexConstructor = new RegExp(patternString, flags);

const searchTerm = "hello";
const dynamicRegex = new RegExp(`^${searchTerm}(\s\w+)?$`, "i");
// Matches 'hello' or 'hello world', case-insensitive
```
When using the constructor with a string pattern, remember that backslashes `\` need to be escaped (e.g., `\d` becomes `"\\d"`).

## 3. RegExp Syntax (Patterns) <a name="regex-syntax"></a>

### Simple Patterns (Literal Characters) <a name="simple-patterns"></a>
The simplest regex consists of literal characters. For example, `/hello/` matches the string "hello".

### Special Characters (Metacharacters) <a name="metacharacters"></a>
Metacharacters have special meanings in regex. To match a metacharacter literally, you must escape it with a backslash `\`.
Common metacharacters: `. \ * + ? ^ $ | [] () {}`

*   **`.` (Wildcard / Dot / Period)**: Matches any single character except newline (unless the `s` flag is used).
    *   `/h.t/` matches "hat", "hot", "h8t", but not "ht" or "heat".
*   **`\` (Escape Character)**: Escapes the next character, giving it a special meaning or making a special character literal.
    *   `/\./` matches a literal dot `.`.
    *   `/\d/` matches a digit (special meaning).

### Character Classes <a name="character-classes"></a>
*   **`[...]` (Set of characters / Character Set)**: Matches any one character from the set.
    *   `/[aeiou]/` matches any lowercase vowel.
    *   `/[0-9a-fA-F]/` matches any hexadecimal digit.
*   **`[^...]` (Negated set)**: Matches any one character *not* in the set.
    *   `/[^0-9]/` matches any non-digit character.
*   **Ranges**: Within a character set, a hyphen `-` can define a range.
    *   `/[a-z]/` matches any lowercase letter.
    *   `/[0-5]/` matches any digit from 0 to 5.
*   **Predefined Character Classes**:
    *   `\d`: Matches any digit (equivalent to `[0-9]`).
    *   `\D`: Matches any non-digit (equivalent to `[^0-9]`).
    *   `\w`: Matches any alphanumeric character (letters, numbers) plus underscore (equivalent to `[A-Za-z0-9_]`).
    *   `\W`: Matches any non-word character (equivalent to `[^A-Za-z0-9_]`).
    *   `\s`: Matches any whitespace character (space, tab, newline, etc.).
    *   `\S`: Matches any non-whitespace character.

### Quantifiers <a name="quantifiers"></a>
Quantifiers specify how many times a character, group, or character class must occur.
*   **`*` (Asterisk)**: Matches the preceding item zero or more times.
    *   `/go*l/` matches "gl", "gol", "gool", "gooool".
*   **`+` (Plus)**: Matches the preceding item one or more times.
    *   `/go+l/` matches "gol", "gool", but not "gl".
*   **`?` (Question Mark)**: Matches the preceding item zero or one time (makes it optional).
    *   `/colou?r/` matches "color" and "colour".
*   **`{n}`**: Matches the preceding item exactly `n` times.
    *   `/\d{3}/` matches exactly three digits (e.g., "123").
*   **`{n,}`**: Matches the preceding item at least `n` times.
    *   `/\d{2,}/` matches two or more digits (e.g., "12", "1234").
*   **`{n,m}`**: Matches the preceding item at least `n` times but not more than `m` times.
    *   `/\d{2,4}/` matches two, three, or four digits.

*   **Greedy vs. Non-Greedy (Lazy) Quantifiers**:
    By default, quantifiers are **greedy**, meaning they match as much text as possible.
    To make a quantifier **non-greedy** (or lazy), append a `?` to it. It will then match as little text as possible.
    *   Greedy: `/a.*b/` on "axbyazb" matches "axbyazb".
    *   Non-Greedy: `/a.*?b/` on "axbyazb" matches "axb" (and then "azb" if global).

### Anchors and Boundaries <a name="anchors-boundaries"></a>
Anchors assert something about the string or the matching process's current position but do not match any characters themselves.
*   **`^` (Caret)**:
    *   Outside a character class `[]`: Matches the beginning of the string.
    *   If the `m` (multiline) flag is enabled, it also matches the beginning of each line.
    *   `/^Hello/` matches "Hello world" but not "World, Hello".
*   **`$` (Dollar sign)**:
    *   Matches the end of the string.
    *   If the `m` (multiline) flag is enabled, it also matches the end of each line.
    *   `/world$/` matches "Hello world" but not "world, Hello".
*   **`\b` (Word Boundary)**: Matches a position between a word character (`\w`) and a non-word character (`\W`), or at the start/end of a string if the first/last character is a word character.
    *   `/\bcat\b/` matches "cat" in "the cat sat" but not in "catalog" or "concatenate".
*   **`\B` (Non-Word Boundary)**: Matches any position that is not a word boundary.
    *   `/\Bcat\B/` matches "cat" in "concatenate" but not in "the cat sat".

### Grouping and Capturing <a name="grouping-capturing"></a>
*   **`(...)` (Capturing Group)**: Parentheses group parts of the pattern and create a capturing group. The matched substring can be remembered and referenced later (e.g., for extraction or backreferences).
    *   `/(\d{2})-(\d{2})-(\d{4})/` for "DD-MM-YYYY" creates three capturing groups.
*   **`(?:...)` (Non-Capturing Group)**: Groups parts of the pattern but does not create a capturing group. Useful for applying quantifiers to a group without capturing it.
    *   `/(?:abc)+/` matches one or more occurrences of "abc" but doesn't capture "abc".
*   **Backreferences (`\1`, `\2`, etc.)**: Refer to the text matched by a previous capturing group within the same regex.
    *   `/(\w+)\s\1/` matches repeated words like "hello hello". `\1` refers to the first captured group.

### Alternation (`|`) <a name="alternation"></a>
The `|` (OR operator) allows matching one of several alternatives.
*   `/cat|dog|fish/` matches "cat" or "dog" or "fish".
*   `/^(apple|banana)$/` matches exactly "apple" or "banana".

### Lookaround (Advanced) <a name="lookaround"></a>
Lookarounds are zero-width assertions, meaning they check for a pattern but don't include it in the match result.
*   **`(?=...)` (Positive Lookahead)**: Asserts that the subpattern `...` follows the current position, without consuming characters.
    *   `/Jack(?=Sprat)/` matches "Jack" only if it's followed by "Sprat".
*   **`(?!...)` (Negative Lookahead)**: Asserts that the subpattern `...` does *not* follow the current position.
    *   `/Windows(?!95|98|NT|2000)/` matches "Windows" not followed by specific versions.
*   **`(?<=...)` (Positive Lookbehind)**: Asserts that the subpattern `...` precedes the current position.
    *   `/(?<=USD)\d+/` matches numbers preceded by "USD".
*   **`(?<!...)` (Negative Lookbehind)**: Asserts that the subpattern `...` does *not* precede the current position.
    *   `/(?<!non-)\w+able/` matches words ending in "able" not preceded by "non-".

## 4. Regular Expression Flags <a name="regex-flags"></a>
Flags modify the behavior of the regular expression. They are placed after the second slash in a literal or as the second argument to the `RegExp` constructor.
*   **`g` (Global search)**: Finds all matches in the string, not just the first one. Methods like `exec()` and `matchAll()` rely on this flag to iterate through matches.
*   **`i` (Case-insensitive search)**: Makes the matching case-insensitive.
*   **`m` (Multiline mode)**: When enabled, `^` and `$` match the start and end of each line (delimited by `\n` or `\r`), not just the start and end of the entire string.
*   **`u` (Unicode mode)**: Enables more complete Unicode support, including matching surrogate pairs correctly and using Unicode property escapes (`\p{...}`, `\P{...}`).
*   **`s` (`dotAll` mode)**: Allows the `.` metacharacter to match newline characters (`\n`, `\r`).
*   **`y` (Sticky search)**: The regex attempts to match only from the `lastIndex` position in the target string. It doesn't skip characters to find a match further in the string.

## 5. Using Regular Expressions in JavaScript <a name="using-regex-js"></a>

### `RegExp` Object Methods <a name="regexp-object-methods"></a>
*   **`test(string)`**: Tests for a match in a string. Returns `true` if a match is found, `false` otherwise.
    ```javascript
    const regex = /hello/i;
    console.log(regex.test("Hello World")); // true
    console.log(regex.test("Hi there"));    // false
    ```
*   **`exec(string)`**: Executes a search for a match in a string. Returns an array with match information (including captured groups) if a match is found, or `null` if no match. If the `g` flag is used, subsequent calls on the same regex object will find subsequent matches (it updates `regex.lastIndex`).
    ```javascript
    const dateRegex = /(\d{4})-(\d{2})-(\d{2})/g;
    const text = "Dates: 2023-01-15, 2024-02-20";
    let match;
    while ((match = dateRegex.exec(text)) !== null) {
      console.log(`Full match: ${match[0]}`); // e.g., 2023-01-15
      console.log(`Year: ${match[1]}, Month: ${match[2]}, Day: ${match[3]}`);
      console.log(`Index: ${match.index}`);
      console.log(`Next search starts at: ${dateRegex.lastIndex}`);
    }
    ```
    The result array from `exec()` has additional properties: `index` (0-based index of the match) and `input` (the original string).

### `String` Object Methods <a name="string-object-methods"></a>
*   **`match(regex)`**: If `regex` does not have the `g` flag, it behaves like `regex.exec(string)`. If `regex` has the `g` flag, it returns an array of all matches (strings), or `null` if no match. Capturing groups are not included in the array when `g` is used (unless `matchAll` is used).
    ```javascript
    const str = "The rain in SPAIN stays mainly in the plain";
    console.log(str.match(/ain/));   // ["ain", index: 5, input: "...", groups: undefined]
    console.log(str.match(/ain/g));  // ["ain", "ain", "ain"]
    console.log(str.match(/xyz/g));  // null
    ```
*   **`matchAll(regex)` (ES2020)**: Returns an iterator of all matches, including capturing groups, even with the `g` flag. The `regex` must have the `g` flag.
    ```javascript
    const textNums = "1a 2b 3c";
    const regexNums = /(\d)(\w)/g;
    for (const match of textNums.matchAll(regexNums)) {
      console.log(`Full: ${match[0]}, Digit: ${match[1]}, Char: ${match[2]}, Index: ${match.index}`);
    }
    // Output for first iteration:
    // Full: 1a, Digit: 1, Char: a, Index: 0
    ```
*   **`search(regex)`**: Searches for a match and returns the index of the first match, or `-1` if no match is found. Ignores the `g` flag.
    ```javascript
    const strHello = "Hello, world!";
    console.log(strHello.search(/world/)); // 7
    console.log(strHello.search(/World/i)); // 7 (case-insensitive)
    console.log(strHello.search(/xyz/));   // -1
    ```
*   **`replace(regex|substring, newSubstring|replacerFunction)`**: Replaces matches with a new substring or the result of a replacer function. If `regex` has the `g` flag, all matches are replaced; otherwise, only the first.
    ```javascript
    const message = "Hello world, hello universe!";
    console.log(message.replace(/hello/gi, "Hi")); // "Hi world, Hi universe!"

    // Using a replacer function
    const prices = "Items: $10, $20, $30";
    const updatedPrices = prices.replace(/\$(\d+)/g, (match, p1) => {
      // match is the full matched string (e.g., "$10")
      // p1 is the first captured group (e.g., "10")
      return "USD " + (parseInt(p1) * 1.1).toFixed(2); // Increase price by 10%
    });
    console.log(updatedPrices); // "Items: USD 11.00, USD 22.00, USD 33.00"

    // Special replacement patterns (e.g., $&, $`, $', $n)
    console.log("abc".replace(/b/, "[$`-$&-$']")); // "a[a-b-c]c"
    // $& inserts the matched substring.
    // $` inserts the portion of the string that precedes the matched substring.
    // $' inserts the portion of the string that follows the matched substring.
    // $n inserts the nth captured group string.
    ```
*   **`split(separator|regex, limit)`**: Splits a string into an array of substrings using a separator (string or regex).
    ```javascript
    console.log("apple,banana,orange".split(",")); // ["apple", "banana", "orange"]
    console.log("one two  three".split(/\s+/));    // ["one", "two", "three"]
    console.log("1a2b3c".split(/\d/));             // ["", "a", "b", "c"]
    ```

## 6. Common Use Cases <a name="regex-common-uses"></a>

### Validating Input <a name="regex-validation"></a>
```javascript
function isValidEmail(email) {
  const emailRegex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/;
  return emailRegex.test(email);
}
console.log(isValidEmail("test@example.com")); // true
console.log(isValidEmail("test@example"));    // false

function isValidPhoneNumber(phone) {
  // Example: North American phone number (simple)
  const phoneRegex = /^\(?(\d{3})\)?[-\s.]?(\d{3})[-\s.]?(\d{4})$/;
  return phoneRegex.test(phone);
}
console.log(isValidPhoneNumber("123-456-7890")); // true
console.log(isValidPhoneNumber("(123) 456.7890")); // true
```

### Searching for Patterns <a name="regex-searching"></a>
```javascript
const logData = "Error: User not found. Warning: Low disk space. Info: System started.";
const errorMessages = logData.match(/Error: [^.]+/g);
console.log(errorMessages); // ["Error: User not found"]
```

### Replacing Text <a name="regex-replacing"></a>
```javascript
let html = "<p>Some <b>bold</b> text and <i>italic</i> text.</p>";
// Remove HTML tags (simple example, not robust for all HTML)
let plainText = html.replace(/<[^>]+>/g, "");
console.log(plainText); // "Some bold text and italic text."
```

### Extracting Data <a name="regex-extracting"></a>
```javascript
const url = "https://www.example.com/path/to/page?query=value#hash";
const urlPartsRegex = /^(https?):\/\/([^\/]+)(\/[^?#]*)?(\?[^#]*)?(#.*)?$/;
const parts = url.match(urlPartsRegex);
if (parts) {
  console.log("Protocol:", parts[1]); // https
  console.log("Domain:", parts[2]);   // www.example.com
  console.log("Path:", parts[3]);     // /path/to/page
  console.log("Query:", parts[4]);    // ?query=value
  console.log("Fragment:", parts[5]); // #hash
}
```

## 7. Best Practices and Pitfalls <a name="regex-best-practices"></a>
*   **Keep it Simple**: Complex regex can be hard to read and debug. Break them down if possible.
*   **Test Thoroughly**: Use tools like RegExr, Regex101, or built-in browser dev tools to test your patterns with various inputs.
*   **Be Specific**: Avoid overly broad patterns (like `.*`) that might match more than intended and lead to performance issues (catastrophic backtracking).
*   **Escape Special Characters**: When matching literal metacharacters, always escape them (e.g., `\.`, `\*`).
*   **Use Non-Capturing Groups `(?:...)`**: If you don't need to capture a group, use a non-capturing group to improve performance slightly and avoid cluttering results.
*   **Understand Greediness**: Be aware of greedy vs. non-greedy quantifiers.
*   **Security (ReDoS)**: Be cautious with regex patterns derived from user input, as poorly crafted or malicious patterns can lead to Regular Expression Denial of Service (ReDoS) attacks due to catastrophic backtracking.
*   **Comments**: For complex regex, consider breaking them down or using comments (some regex engines support comments, or comment them in your code).

## 8. Key Takeaways <a name="key-takeaways-regex"></a>
*   Regular expressions are patterns for matching character combinations in strings.
*   They can be created using literals (`/pattern/flags`) or the `RegExp` constructor.
*   Syntax includes literal characters, metacharacters, character classes, quantifiers, anchors, and groups.
*   Flags (`g`, `i`, `m`, `u`, `s`, `y`) modify matching behavior.
*   `RegExp` methods (`test()`, `exec()`) and `String` methods (`match()`, `matchAll()`, `search()`, `replace()`, `split()`) utilize regex.
*   Regex is powerful for validation, searching, replacing, and extracting data.
*   Write clear, tested, and efficient regular expressions, being mindful of potential pitfalls like ReDoS.

---

⬅️ [Back to Fetch API](../15-js-fetch-api/README.md) | ➡️ [Next Section: JavaScript Object Methods and `this` Deep Dive (Review & Advanced)](../17-js-object-methods-this-advanced/README.md)
