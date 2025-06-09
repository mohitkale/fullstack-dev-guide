# 33. TypeScript and Internationalization (i18n)

Internationalization (i18n) is the process of designing software applications so that they can be adapted to various languages and regions without engineering changes. Localization (l10n) is the process of adapting internationalized software for a specific region or language by adding locale-specific components and translating text. TypeScript can significantly enhance i18n efforts by providing type safety for translation keys, interpolation parameters, and formatting functions.

This tutorial explores how to build type-safe i18n solutions with TypeScript, covering popular libraries, managing translations, and ensuring localized content is handled correctly.

## Table of Contents
1.  [Why TypeScript for i18n?](#why-ts-i18n)
    *   Preventing missing or misspelled translation keys
    *   Type-safe interpolation parameters
    *   Ensuring correct pluralization rules
    *   Improved refactoring of translation resources
2.  [Managing Translation Keys Type-Safely](#managing-translation-keys-ts)
    *   Using objects or enums for keys
    *   Generating types from translation files (e.g., JSON, YAML)
    *   Nested translation keys
3.  [Type-Safe Interpolation and Pluralization](#type-safe-interpolation-pluralization)
    *   Ensuring correct types and presence of variables for interpolation
    *   Handling plural forms with type safety
4.  [Popular i18n Libraries with TypeScript Support](#popular-i18n-libraries-ts)
    *   [**i18next** (and `react-i18next`)](#i18next-ts)
        *   Setup and configuration with TypeScript
        *   Typing resources and the `t` function
        *   Using `i18next-typescript` or custom type generation
    *   [**FormatJS (React Intl)**](#formatjs-ts)
        *   Leveraging ICU Message Format
        *   Typed components (`<FormattedMessage />`) and hooks (`useIntl`)
    *   [**LinguiJS**](#lingui-ts)
        *   Using macros for message extraction
        *   Type generation for message catalogs
    *   [**typesafe-i18n**](#typesafe-i18n-lib)
        *   A lightweight, type-safe i18n library specifically for TypeScript
5.  [Structuring Translation Files](#structuring-translation-files)
    *   Organizing by language, feature, or component
    *   Common formats: JSON, YAML, PO files
6.  [Generating Types from Translation Files](#generating-types-from-translations)
    *   Benefits of automated type generation
    *   Tools and scripts (e.g., `i18next-parser`, custom scripts, library-specific tools)
7.  [Type-Safe Date, Number, and Currency Formatting](#type-safe-formatting)
    *   Using the `Intl` API with TypeScript
    *   Wrapper functions for consistent, type-safe formatting
8.  [Handling Right-to-Left (RTL) Languages](#rtl-support-ts)
    *   Considerations for UI and text direction
    *   Using TypeScript to manage direction-specific logic or styles (less direct, more structural)
9.  [Testing i18n with TypeScript](#testing-i18n-ts)
    *   Unit testing translation functions
    *   Integration testing localized components
    *   Mocking i18n libraries in tests
10. [Best Practices for Type-Safe i18n](#best-practices-ts-i18n)
11. [Key Takeaways](#key-takeaways-ts-i18n)

## 1. Why TypeScript for i18n? <a name="why-ts-i18n"></a>
TypeScript brings significant advantages to i18n workflows:
*   **Prevents Missing/Misspelled Keys:** Compile-time checks ensure that you're using valid translation keys. No more runtime errors or blank spots due to typos.
*   **Type-Safe Interpolation:** If a translation string expects variables (e.g., `Hello {{name}}!`), TypeScript can ensure you provide them and that they are of the correct type.
*   **Correct Pluralization:** For languages with complex plural rules, TypeScript can help ensure you're providing the necessary data (e.g., a count) for the i18n library to pick the correct form.
*   **Improved Refactoring:** When translation keys or structures change, the TypeScript compiler will guide you to update all usages.

## 2. Managing Translation Keys Type-Safely <a name="managing-translation-keys-ts"></a>

### Using Objects for Keys (Manual Approach)
```typescript
// translations/en.ts
export const en = {
  greeting: "Hello",
  user: {
    welcome: "Welcome, {{name}}!",
    posts: "You have {{count}} post(s)."
  }
};

// translations/index.ts
import { en } from './en';
// import { es } from './es'; // etc.

export type TranslationKeys = typeof en; // Use one language as the source of truth for keys

// Usage (conceptual)
function translate(key: keyof TranslationKeys /* or more specific path type */) {
  // ...
}
```
This approach is simple but requires manual maintenance of the `TranslationKeys` type if keys diverge or if you want deep key access.

### Generating Types from Translation Files
This is the most robust approach. Translation files (e.g., JSON) are the source of truth, and types are generated from them.

```json
// locales/en.json
{
  "common": {
    "appName": "My Awesome App"
  },
  "userProfile": {
    "title": "User Profile",
    "greeting": "Hello, {{name}}!"
  }
}
```
Tools like `i18next-typescript` or custom scripts can parse these JSON files and generate corresponding TypeScript types/interfaces for keys and expected interpolation values.

## 3. Type-Safe Interpolation and Pluralization <a name="type-safe-interpolation-pluralization"></a>
With generated types, your i18n function can be strongly typed:

```typescript
// Conceptual example with generated types
interface TranslationFunctions {
  t: {
    (key: 'common.appName'): string;
    (key: 'userProfile.title'): string;
    (key: 'userProfile.greeting', params: { name: string }): string;
    // ... other keys and params
  };
}

const i18n: TranslationFunctions = getI18nInstance(); // From your i18n library

console.log(i18n.t('common.appName'));
console.log(i18n.t('userProfile.greeting', { name: 'Alice' }));
// console.log(i18n.t('userProfile.greeting')); // Error: Expected 2 arguments, but got 1.
// console.log(i18n.t('userProfile.greeting', { username: 'Alice' })); // Error: 'username' does not exist in type '{ name: string; }'
```
For pluralization, types can ensure a `count` variable (or similar) is provided when a key expects it.

## 4. Popular i18n Libraries with TypeScript Support <a name="popular-i18n-libraries-ts"></a>

### **i18next** (and `react-i18next`) <a name="i18next-ts"></a>
`i18next` is a very popular and flexible i18n framework.
*   **Typing Resources:** You can define an interface for your translation resources and use declaration merging to type `i18next`.
    ```typescript
    // src/i18n/i18next.d.ts
    import 'i18next';
    import en from './locales/en.json'; // Your English translations
    import es from './locales/es.json';

    declare module 'i18next' {
      interface CustomTypeOptions {
        defaultNS: 'common'; // Default namespace
        resources: {
          common: typeof en.common; // Assuming 'common' is a top-level key in your JSON
          userProfile: typeof en.userProfile;
          // Add other namespaces
        };
      }
    }
    ```
*   The `t` function from `useTranslation` (react-i18next) or `i18next.t` will then be type-aware based on these definitions.
*   Tools like `i18next-parser` can extract keys, and `i18next-typescript` can generate the `.d.ts` file.

### **FormatJS (React Intl)** <a name="formatjs-ts"></a>
FormatJS uses the ICU Message Format, which is powerful for handling plurals, genders, and complex interpolations.
*   It provides typed components like `<FormattedMessage />` and hooks like `useIntl()`.
*   Types for message IDs and values can be generated using `@formatjs/cli`.
    ```typescript
    // Example with useIntl
    import { useIntl } from 'react-intl';

    // Assuming message IDs are generated or defined
    // messages.ts
    // defineMessages({ greeting: { id: 'app.greeting', defaultMessage: 'Hello, {name}!' }})

    function MyComponent({ name }: { name: string }) {
      const intl = useIntl();
      const message = intl.formatMessage({ id: 'app.greeting' }, { name });
      // `id` would ideally be from a typed set of IDs
      // `name` in the second arg is type-checked if types are generated for values
      return <p>{message}</p>;
    }
    ```

### **LinguiJS** <a name="lingui-ts"></a>
LinguiJS uses macros (`t`, `plural`, `<Trans>`) to mark messages directly in your code. The CLI then extracts these into message catalogs.
*   It generates types for message catalogs, providing type safety when accessing compiled messages.

### **typesafe-i18n** <a name="typesafe-i18n-lib"></a>
This library is built from the ground up with TypeScript in mind.
*   It generates fully typed i18n functions based on your translation files.
*   Offers excellent autocompletion for keys and interpolation parameters.
*   Supports formatters for dates, numbers, etc.

## 5. Structuring Translation Files <a name="structuring-translation-files"></a>
*   **By Language:** A common approach is a folder per language (e.g., `locales/en/common.json`, `locales/es/common.json`).
*   **By Feature/Namespace:** Within each language folder, you might further divide translations by application feature or domain (e.g., `locales/en/user.json`, `locales/en/product.json`). This aligns well with i18next namespaces.
*   **Formats:** JSON is very common. YAML is also used for its readability. PO files are standard in some ecosystems (e.g., Gettext).

## 6. Generating Types from Translation Files <a name="generating-types-from-translations"></a>
Automating type generation is key to maintaining type safety as translations evolve.
*   Define one language (e.g., English) as the canonical source of keys and structure.
*   Use a script or tool that:
    1.  Parses the canonical translation file(s).
    2.  Generates TypeScript interfaces or type aliases for the keys.
    3.  Optionally, generates types for expected interpolation parameters based on placeholders (e.g., `{{name}}` implies a `name: string` parameter).
*   Integrate this script into your build process or as a pre-commit hook.

## 7. Type-Safe Date, Number, and Currency Formatting <a name="type-safe-formatting"></a>
The built-in `Intl` object in JavaScript provides powerful localization capabilities.
```typescript
function formatDate(date: Date, locale: string = 'en-US'): string {
  return new Intl.DateTimeFormat(locale, { 
    year: 'numeric', 
    month: 'long', 
    day: 'numeric' 
  }).format(date);
}

function formatCurrency(value: number, currency: string, locale: string = 'en-US'): string {
  return new Intl.NumberFormat(locale, {
    style: 'currency',
    currency: currency, // e.g., 'USD', 'EUR'
  }).format(value);
}

console.log(formatDate(new Date())); // e.g., "June 10, 2025"
console.log(formatCurrency(1234.56, 'USD')); // e.g., "$1,234.56"
```
TypeScript ensures you pass a `Date` object to `formatDate` and `number` to `formatCurrency`. You can create more specific typed wrappers for your application's needs.

## 8. Handling Right-to-Left (RTL) Languages <a name="rtl-support-ts"></a>
While TypeScript doesn't directly manage CSS for RTL, it can help in structuring your code to handle directionality:
*   You might have types or enums for text direction (`'ltr'` | `'rtl'`).
*   Components could accept a direction prop, influencing their rendering or style application.
*   Type safety can ensure that direction-specific logic is consistently applied.

## 9. Testing i18n with TypeScript <a name="testing-i18n-ts"></a>
*   **Unit Tests:** Test your translation utility functions. Ensure they return correct strings for given keys and parameters.
*   **Component Tests:** When testing UI components, verify that they render correctly localized text. You might need to mock the i18n provider or pass a specific locale/translations to your component during testing.
*   TypeScript helps ensure your test setups (e.g., mock translation objects) match the expected types.

## 10. Best Practices for Type-Safe i18n <a name="best-practices-ts-i18n"></a>
*   **Single Source of Truth:** Your translation files (e.g., `en.json`) should be the single source of truth for keys and base translations.
*   **Automate Type Generation:** Don't rely on manually updating TypeScript types for your translations. Use tools or scripts.
*   **Strict Key Usage:** Avoid dynamic or concatenated translation keys if they bypass type checking. If dynamic keys are necessary, ensure there's a well-typed fallback or validation.
*   **Type Interpolation Parameters:** Clearly define the expected types for any variables in your translation strings.
*   **Integrate with Linters:** Some i18n libraries offer ESLint plugins to catch issues like missing keys.
*   **Keep Translations Updated:** Ensure all language files are synchronized with the keys defined in your source-of-truth language.

## 11. Key Takeaways <a name="key-takeaways-ts-i18n"></a>
*   TypeScript significantly improves the robustness of i18n by providing compile-time checks for translation keys, parameters, and formatting.
*   Automated type generation from translation files is crucial for maintaining type safety as your application grows.
*   Popular i18n libraries like `i18next`, `FormatJS`, and `LinguiJS` offer good TypeScript support, often enhanced by community tools or built-in CLI features.
*   `typesafe-i18n` is a noteworthy option for projects prioritizing a TypeScript-first i18n experience.
*   Beyond basic string translation, TypeScript helps in type-safely handling dates, numbers, plurals, and other locale-sensitive formatting.

By adopting these practices, you can build applications that are not only adaptable to global audiences but also more maintainable and less prone to i18n-related bugs.

---

⬅️ [Back to TypeScript Performance Optimization Techniques](../32-ts-performance-optimization/README.md) | ➡️ [Next: TypeScript in Monorepos: Strategies and Tooling](../34-ts-monorepos/README.md) *(Placeholder)*
