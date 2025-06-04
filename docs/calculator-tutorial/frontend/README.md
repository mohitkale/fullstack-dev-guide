# Frontend Calculator with React + Vite

This guide will walk you through creating a simple calculator application using React and TypeScript with Vite. By the end of this tutorial, you'll have a functional calculator that can perform basic arithmetic operations.

## Table of Contents
1. [Project Setup](#1-project-setup)
2. [Creating the Calculator Component](#2-creating-the-calculator-component)
3. [Styling the Calculator](#3-styling-the-calculator)
4. [Adding Calculator Logic](#4-adding-calculator-logic)
5. [Testing the Calculator](#5-testing-the-calculator)

## 1. Project Setup

First, let's create a new Vite project with React and TypeScript:

```bash
# Create a new Vite project with React and TypeScript
npm create vite@latest calculator-frontend -- --template react-ts

# Navigate to the project directory
cd calculator-frontend

# Install dependencies
npm install

# Start the development server
npm run dev
```

This will set up a new React project with TypeScript and start the development server. You can view your app at `http://localhost:5173`.

## 2. Creating the Calculator Component

Let's create a new file for our calculator component:

```tsx
// src/components/Calculator.tsx
import { useState } from 'react';

// Define the operations our calculator will support
type Operation = '+' | '-' | '*' | '/' | '%' | '';

export function Calculator() {
  // State to store the current input value
  const [currentInput, setCurrentInput] = useState('0');
  // State to store the previous input value
  const [previousInput, setPreviousInput] = useState('');
  // State to store the current operation
  const [operation, setOperation] = useState<Operation>('');
  // Flag to track if we should reset the input on next number press
  const [shouldResetInput, setShouldResetInput] = useState(false);

  // Handle number button clicks
  const handleNumberClick = (num: string) => {
    if (shouldResetInput || currentInput === '0') {
      setCurrentInput(num);
      setShouldResetInput(false);
    } else {
      setCurrentInput(`${currentInput}${num}`);
    }
  };

  // Handle operation button clicks
  const handleOperation = (op: Operation) => {
    if (currentInput === '0' && previousInput) {
      setOperation(op);
      return;
    }

    if (previousInput) {
      const result = calculate();
      setCurrentInput(String(result));
      setPreviousInput(String(result));
    } else {
      setPreviousInput(currentInput);
    }
    
    setOperation(op);
    setShouldResetInput(true);
  };

  // Handle equals button click
  const handleEquals = () => {
    if (!operation || !previousInput) return;
    
    const result = calculate();
    setCurrentInput(String(result));
    setPreviousInput('');
    setOperation('');
    setShouldResetInput(true);
  };

  // Handle clear button click
  const handleClear = () => {
    setCurrentInput('0');
    setPreviousInput('');
    setOperation('');
    setShouldResetInput(false);
  };

  // Handle decimal point
  const handleDecimal = () => {
    if (shouldResetInput) {
      setCurrentInput('0.');
      setShouldResetInput(false);
      return;
    }
    
    if (!currentInput.includes('.')) {
      setCurrentInput(`${currentInput}.`);
    }
  };

  // Calculate the result based on the current operation
  const calculate = (): number => {
    const prev = parseFloat(previousInput);
    const current = parseFloat(currentInput);
    
    if (isNaN(prev) || isNaN(current)) return 0;

    switch (operation) {
      case '+':
        return prev + current;
      case '-':
        return prev - current;
      case '*':
        return prev * current;
      case '/':
        return prev / current;
      case '%':
        return prev % current;
      default:
        return current;
    }
  };

  // Format the display value
  const getDisplayValue = () => {
    if (previousInput && operation) {
      return `${previousInput} ${operation} ${shouldResetInput ? '' : currentInput}`;
    }
    return currentInput;
  };

  return (
    <div className="calculator">
      <div className="display">{getDisplayValue()}</div>
      <div className="buttons">
        <button className="span-2" onClick={handleClear}>AC</button>
        <button onClick={() => handleOperation('%')}>%</button>
        <button onClick={() => handleOperation('/')}>/</button>
        
        <button onClick={() => handleNumberClick('7')}>7</button>
        <button onClick={() => handleNumberClick('8')}>8</button>
        <button onClick={() => handleNumberClick('9')}>9</button>
        <button onClick={() => handleOperation('*')}>*</button>
        
        <button onClick={() => handleNumberClick('4')}>4</button>
        <button onClick={() => handleNumberClick('5')}>5</button>
        <button onClick={() => handleNumberClick('6')}>6</button>
        <button onClick={() => handleOperation('-')}>-</button>
        
        <button onClick={() => handleNumberClick('1')}>1</button>
        <button onClick={() => handleNumberClick('2')}>2</button>
        <button onClick={() => handleNumberClick('3')}>3</button>
        <button onClick={() => handleOperation('+')}>+</button>
        
        <button className="span-2" onClick={() => handleNumberClick('0')}>0</button>
        <button onClick={handleDecimal}>.</button>
        <button onClick={handleEquals}>=</button>
      </div>
    </div>
  );
}

export default Calculator;
```

## 3. Styling the Calculator

Let's add some basic styling to make our calculator look good. Create a new CSS file:

```css
/* src/components/Calculator.css */
.calculator {
  width: 300px;
  margin: 50px auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  background-color: #f5f5f5;
}

.display {
  background-color: #fff;
  padding: 15px;
  margin-bottom: 15px;
  text-align: right;
  font-size: 24px;
  border: 1px solid #ddd;
  border-radius: 5px;
  min-height: 36px;
  word-wrap: break-word;
}

.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

button {
  padding: 15px;
  font-size: 18px;
  border: none;
  border-radius: 5px;
  background-color: #e0e0e0;
  cursor: pointer;
  transition: background-color 0.2s;
}

button:hover {
  background-color: #d0d0d0;
}

button:active {
  background-color: #c0c0c0;
}

.span-2 {
  grid-column: span 2;
}
```

## 4. Updating the App Component

Now, let's update the main App component to use our Calculator:

```tsx
// src/App.tsx
import { Calculator } from './components/Calculator';
import './App.css';

function App() {
  return (
    <div className="app">
      <h1>React Calculator</h1>
      <Calculator />
    </div>
  );
}

export default App;
```

And update the App.css:

```css
/* src/App.css */
.app {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
  font-family: Arial, sans-serif;
}

h1 {
  color: #333;
  margin-bottom: 30px;
}
```

## 5. Testing the Calculator

Now that we've built our calculator, let's test it out:

1. Start the development server if it's not already running:
   ```bash
   npm run dev
   ```

2. Open your browser and navigate to `http://localhost:5173`

3. Try the following calculations:
   - Addition: `2 + 2 =` (should display 4)
   - Subtraction: `10 - 5 =` (should display 5)
   - Multiplication: `6 * 7 =` (should display 42)
   - Division: `15 / 3 =` (should display 5)
   - Modulus: `10 % 3 =` (should display 1)
   - Decimal numbers: `0.1 + 0.2 =` (should display 0.3)
   - Chained operations: `2 + 3 * 4 =` (should display 14, not 20)

## 6. How It Works

1. **State Management**:
   - `currentInput`: Stores the current number being entered
   - `previousInput`: Stores the previous number in the calculation
   - `operation`: Stores the current operation (+, -, *, /, %)
   - `shouldResetInput`: Tracks whether to reset the input on the next number press

2. **Key Functions**:
   - `handleNumberClick`: Handles number button presses
   - `handleOperation`: Handles operation button presses
   - `handleEquals`: Calculates and displays the result
   - `handleClear`: Resets the calculator
   - `handleDecimal`: Handles decimal point input
   - `calculate`: Performs the actual calculation

3. **Styling**:
   - Uses CSS Grid for the calculator buttons
   - Responsive design that works on different screen sizes
   - Hover and active states for better user feedback

## 7. Next Steps

Now that you've built a basic calculator, here are some ideas for improvements:

1. Add keyboard support for better accessibility
2. Implement a history of calculations
3. Add scientific calculator functions (square root, power, etc.)
4. Add unit tests using React Testing Library
5. Add animations for button presses

## 8. Conclusion

Congratulations! You've built a fully functional calculator using React and TypeScript. This project covered:

- React component structure
- State management with useState
- Event handling
- Basic arithmetic operations
- Styling with CSS
- Component composition

Feel free to extend this project further or use it as a starting point for more complex applications.
