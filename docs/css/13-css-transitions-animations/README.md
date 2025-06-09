# 13. CSS Transitions and Animations

CSS Transitions and Animations allow you to add motion and visual feedback to your web elements, making user interfaces more engaging and intuitive. Transitions provide a way to control animation speed when changing CSS properties, while animations allow for more complex, multi-step sequences.

## Table of Contents
1.  [CSS Transitions](#css-transitions)
    *   [Introduction to Transitions](#intro-transitions)
    *   [Transition Properties](#transition-properties)
        *   [`transition-property`](#transition-property)
        *   [`transition-duration`](#transition-duration)
        *   [`transition-timing-function`](#transition-timing-function)
        *   [`transition-delay`](#transition-delay)
    *   [`transition` (Shorthand Property)](#transition-shorthand)
    *   [Triggering Transitions (e.g., on `:hover`, `:focus`, class change)](#triggering-transitions)
    *   [Transitioning Multiple Properties](#transitioning-multiple)
    *   [What Can Be Transitioned?](#what-can-be-transitioned)
2.  [CSS Animations](#css-animations)
    *   [Introduction to Animations](#intro-animations)
    *   [Defining Animations with `@keyframes`](#keyframes)
    *   [Animation Properties](#animation-properties)
        *   [`animation-name`](#animation-name)
        *   [`animation-duration`](#animation-duration)
        *   [`animation-timing-function`](#animation-timing-function)
        *   [`animation-delay`](#animation-delay)
        *   [`animation-iteration-count`](#animation-iteration-count)
        *   [`animation-direction`](#animation-direction)
        *   [`animation-fill-mode`](#animation-fill-mode)
        *   [`animation-play-state`](#animation-play-state)
    *   [`animation` (Shorthand Property)](#animation-shorthand)
    *   [Examples of Animations](#animation-examples)
3.  [Transitions vs. Animations](#transitions-vs-animations)
4.  [Performance Considerations](#performance-considerations)
    *   [Animating `transform` and `opacity`](#animating-transform-opacity)
    *   [Hardware Acceleration](#hardware-acceleration)
5.  [Accessibility Considerations](#accessibility-considerations-motion)
    *   [The `prefers-reduced-motion` Media Query](#prefers-reduced-motion)
6.  [Key Takeaways](#key-takeaways-motion)

## 1. CSS Transitions <a name="css-transitions"></a>

### Introduction to Transitions <a name="intro-transitions"></a>
CSS transitions allow property changes to occur smoothly over a specified duration, rather than happening instantly. This is commonly used for effects like hover states on buttons, smooth opening/closing of menus, or subtle visual feedback.

### Transition Properties <a name="transition-properties"></a>

#### `transition-property` <a name="transition-property"></a>
*   Specifies the name(s) of the CSS properties to which a transition effect should be applied.
*   Values: `none`, `all` (default), or a specific property name (e.g., `width`, `background-color`, `transform`).
    ```css
    .box {
      transition-property: background-color;
    }
    ```

#### `transition-duration` <a name="transition-duration"></a>
*   Specifies the length of time a transition animation should take to complete.
*   Values: Time in seconds (`s`) or milliseconds (`ms`). Default is `0s` (no transition).
    ```css
    .box {
      transition-property: opacity;
      transition-duration: 0.5s; /* Transition will take 0.5 seconds */
    }
    ```

#### `transition-timing-function` <a name="transition-timing-function"></a>
*   Describes how the intermediate values of the CSS properties being affected by a transition effect are calculated. This allows an animation to speed up and slow down over its course.
*   Common Values:
    *   `ease` (default): Slow start, then fast, then end slowly.
    *   `linear`: Animates at an even speed.
    *   `ease-in`: Slow start.
    *   `ease-out`: Slow end.
    *   `ease-in-out`: Slow start and end.
    *   `cubic-bezier(n,n,n,n)`: Define your own timing function.
    ```css
    .box {
      transition-property: transform;
      transition-duration: 1s;
      transition-timing-function: ease-in-out;
    }
    ```

#### `transition-delay` <a name="transition-delay"></a>
*   Specifies a delay (in seconds or milliseconds) before the transition effect will start.
*   Default is `0s` (no delay).
    ```css
    .box {
      transition-property: width;
      transition-duration: 0.3s;
      transition-delay: 0.1s; /* Transition starts after 0.1 seconds */
    }
    ```

### `transition` (Shorthand Property) <a name="transition-shorthand"></a>
*   A shorthand property for setting `transition-property`, `transition-duration`, `transition-timing-function`, and `transition-delay` in a single declaration.
*   Order (recommended, though flexible for duration/delay):
    `transition: <property> <duration> <timing-function> <delay>;`
    ```css
    .button {
      background-color: blue;
      padding: 10px 20px;
      transition: background-color 0.3s ease-out 0s, padding 0.2s linear 0.1s;
      /* or simply for one property: */
      /* transition: all 0.5s ease; */
    }
    .button:hover {
      background-color: darkblue;
      padding: 12px 24px;
    }
    ```

### Triggering Transitions <a name="triggering-transitions"></a>
Transitions typically start when the value of a CSS property changes. Common triggers include:
*   Pseudo-classes: `:hover`, `:focus`, `:active`, `:checked`.
*   JavaScript: Adding or removing a class that defines different property values.

```css
/* Trigger on hover */
.element {
  width: 100px;
  height: 100px;
  background-color: lightcoral;
  transition: width 0.5s ease, background-color 0.5s ease;
}
.element:hover {
  width: 150px;
  background-color: coral;
}
```

### Transitioning Multiple Properties <a name="transitioning-multiple"></a>
You can transition multiple properties by listing them, separated by commas, in the `transition-property` or the `transition` shorthand.

```css
.box {
  transition-property: transform, opacity;
  transition-duration: 0.5s, 1s; /* transform takes 0.5s, opacity takes 1s */
}
/* Or using shorthand: */
.box {
  transition: transform 0.5s ease-in-out, opacity 1s linear 0.2s;
}
```
If you provide fewer durations, timing functions, or delays than properties, the list of values will be repeated.

### What Can Be Transitioned? <a name="what-can-be-transitioned"></a>
Not all CSS properties can be transitioned. Generally, properties that have identifiable intermediate values can be transitioned. This includes:
*   Numeric values: `width`, `height`, `opacity`, `font-size`, `margin`, `padding`, `border-radius`.
*   Colors: `color`, `background-color`, `border-color`.
*   Transforms: `transform` (e.g., `translate`, `scale`, `rotate`).
*   Shadows: `box-shadow`, `text-shadow`.

Properties like `display`, `font-family` (generally), or `background-image` (for gradients, mostly) cannot be smoothly transitioned as they don't have a clear in-between state.

## 2. CSS Animations <a name="css-animations"></a>

### Introduction to Animations <a name="intro-animations"></a>
CSS Animations provide more control over animation sequences than transitions. They allow you to create multi-step animations using keyframes, control iteration counts, animation direction, and more. Animations can start automatically without needing a trigger like a hover state.

### Defining Animations with `@keyframes` <a name="keyframes"></a>
Keyframes define the stages and styles of an animation sequence. Each `@keyframes` rule specifies the animation's behavior at various points during its duration.

*   `from` and `to` keywords can be used for simple two-step animations (equivalent to `0%` and `100%`).
*   Percentages (`0%` to `100%`) define intermediate steps.

```css
@keyframes slide-in {
  from { /* or 0% */
    transform: translateX(-100%);
    opacity: 0;
  }
  to { /* or 100% */
    transform: translateX(0);
    opacity: 1;
  }
}

@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.1);
    opacity: 0.7;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}
```

### Animation Properties <a name="animation-properties"></a>
These properties are applied to the element you want to animate.

#### `animation-name` <a name="animation-name"></a>
*   Specifies the name of the `@keyframes` rule to apply.
    ```css
    .element {
      animation-name: slide-in;
    }
    ```

#### `animation-duration` <a name="animation-duration"></a>
*   Specifies the length of time an animation should take to complete one cycle.
*   Values: Time in seconds (`s`) or milliseconds (`ms`).
    ```css
    .element {
      animation-name: pulse;
      animation-duration: 2s;
    }
    ```

#### `animation-timing-function` <a name="animation-timing-function"></a>
*   Describes how an animation progresses over one cycle. Same values as `transition-timing-function` (`ease`, `linear`, `cubic-bezier()`, etc.).
    ```css
    .element {
      animation-timing-function: ease-out;
    }
    ```

#### `animation-delay` <a name="animation-delay"></a>
*   Specifies a delay before the animation will start.
    ```css
    .element {
      animation-delay: 0.5s;
    }
    ```

#### `animation-iteration-count` <a name="animation-iteration-count"></a>
*   Specifies the number of times an animation cycle should be played.
*   Values: A number (e.g., `3`) or `infinite`.
    ```css
    .element {
      animation-iteration-count: infinite;
    }
    ```

#### `animation-direction` <a name="animation-direction"></a>
*   Specifies whether an animation should play forwards, backwards, or alternate cycles.
*   Values:
    *   `normal` (default): Plays forwards each cycle.
    *   `reverse`: Plays backwards each cycle.
    *   `alternate`: Plays forwards first, then backwards, then forwards, etc.
    *   `alternate-reverse`: Plays backwards first, then forwards, then backwards, etc.
    ```css
    .element {
      animation-direction: alternate;
    }
    ```

#### `animation-fill-mode` <a name="animation-fill-mode"></a>
*   Specifies how a CSS animation should apply styles to its target before and after its execution.
*   Values:
    *   `none` (default): Animation will not apply any styles to the element before or after it is executing.
    *   `forwards`: The element will retain the style values from the last keyframe when the animation ends.
    *   `backwards`: The element will get the style values from the first keyframe as soon as the animation is applied (before `animation-delay`), and retain this during the delay period.
    *   `both`: The animation will follow the rules for both `forwards` and `backwards`.
    ```css
    .element {
      animation-fill-mode: forwards;
    }
    ```

#### `animation-play-state` <a name="animation-play-state"></a>
*   Specifies whether an animation is running or paused.
*   Values: `running` (default), `paused`.
*   Can be changed with JavaScript to control animations.
    ```css
    .element {
      animation-play-state: paused; /* Animation will not run until changed */
    }
    .element:hover {
      animation-play-state: running;
    }
    ```

### `animation` (Shorthand Property) <a name="animation-shorthand"></a>
*   A shorthand property for setting all animation properties in a single declaration.
*   Order (common practice, though flexible for duration/delay/etc.):
    `animation: <name> <duration> <timing-function> <delay> <iteration-count> <direction> <fill-mode> <play-state>;`
    ```css
    .spinner {
      animation: spin 1s linear 0s infinite normal none running;
      /* A more concise common example: */
      /* animation: spin 1s linear infinite; */
    }

    @keyframes spin {
      from { transform: rotate(0deg); }
      to { transform: rotate(360deg); }
    }
    ```

### Examples of Animations <a name="animation-examples"></a>

**Loading Spinner:**
```css
.loader {
  width: 50px;
  height: 50px;
  border: 5px solid #f3f3f3; /* Light grey */
  border-top: 5px solid #3498db; /* Blue */
  border-radius: 50%;
  animation: spin 1.5s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
```

**Fade In Text:**
```css
.fade-in-text {
  animation: fadeIn 2s ease-in-out 0.5s 1 normal forwards;
}

@keyframes fadeIn {
  0% { opacity: 0; }
  100% { opacity: 1; }
}
```

## 3. Transitions vs. Animations <a name="transitions-vs-animations"></a>

| Feature          | CSS Transitions                                  | CSS Animations                                       |
|------------------|--------------------------------------------------|------------------------------------------------------|
| **Purpose**      | Simple state changes (e.g., hover effects)       | Complex, multi-step sequences                        |
| **Control**      | Limited to start and end states                  | Fine-grained control with keyframes (`0%` to `100%`) |
| **Trigger**      | Requires a state change (e.g., hover, JS class)  | Can start automatically on load, can be paused/resumed |
| **Iteration**    | Typically runs once per trigger                  | Can loop multiple times or infinitely                |
| **Complexity**   | Simpler to implement for basic effects           | More powerful for intricate animations               |

**When to use which:**
*   Use **transitions** for simple interactions and state changes, like hover effects on buttons, smooth menu reveals, or visual feedback to user actions.
*   Use **animations** for more complex scenarios like loading spinners, attention-grabbing effects, character animations, or sequences that need to run automatically or loop.

## 4. Performance Considerations <a name="performance-considerations"></a>

Poorly implemented animations can lead to janky performance and high CPU usage.

### Animating `transform` and `opacity` <a name="animating-transform-opacity"></a>
*   For the smoothest animations, try to animate only `transform` (e.g., `translate`, `scale`, `rotate`) and `opacity`.
*   These properties can often be handled by the browser's compositor thread, meaning they don't trigger expensive layout recalculations or repaints of the page.
*   Animating properties like `width`, `height`, `margin`, `padding`, `top`, `left` often causes layout changes, which are more performance-intensive.

### Hardware Acceleration <a name="hardware-acceleration"></a>
*   Browsers can use the GPU (Graphics Processing Unit) to accelerate animations of certain properties, especially `transform` (specifically 3D transforms like `translate3d`, `scale3d`) and `opacity`.
*   Sometimes, applying a simple 3D transform like `transform: translateZ(0);` or `will-change: transform;` can hint to the browser to move the element to its own layer and use hardware acceleration. However, use `will-change` sparingly as it can consume memory.

## 5. Accessibility Considerations <a name="accessibility-considerations-motion"></a>

While animations can enhance UX, they can also be problematic for users with vestibular disorders, motion sensitivity, or cognitive impairments.

### The `prefers-reduced-motion` Media Query <a name="prefers-reduced-motion"></a>
*   This media query allows users to indicate their preference for reduced motion in their operating system settings.
*   You should respect this preference by disabling or reducing non-essential animations.

```css
.animated-element {
  animation: my-animation 2s infinite;
}

@media (prefers-reduced-motion: reduce) {
  .animated-element {
    animation: none; /* Disable animation */
    /* Or provide a simpler, less distracting alternative */
    /* transition: opacity 0.5s; */
  }
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important; /* Play only once and very quickly */
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important; /* Disable smooth scrolling */
  }
}
```

## 6. Key Takeaways <a name="key-takeaways-motion"></a>
*   **Transitions** are for simple, two-state animations triggered by property changes.
*   **Animations** use `@keyframes` for complex, multi-step sequences and offer more control (iteration, direction, etc.).
*   Master the shorthand properties (`transition` and `animation`) for conciseness.
*   Prioritize animating `transform` and `opacity` for better performance.
*   Always consider accessibility by respecting the `prefers-reduced-motion` media query.
*   Transitions and animations can significantly improve user experience when used thoughtfully.

---

⬅️ [Back to CSS Responsive Web Design](../12-css-responsive-design/README.md) | ➡️ [Next Section: CSS Best Practices](../14-css-best-practices/README.md)
