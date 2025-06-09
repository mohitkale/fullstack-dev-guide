# 50. TypeScript Project: Building a Simple Task Manager App

Welcome to this hands-on project tutorial! We'll build a simple command-line (CLI) Task Manager application using TypeScript. This project will help solidify your understanding of various TypeScript concepts covered in this series by applying them in a practical context.

## Table of Contents
1.  [Introduction & Project Goals](#intro-project-goals)
2.  [Prerequisites](#prerequisites)
3.  [Step 1: Project Setup](#step1-project-setup)
    *   Initialize npm
    *   Install TypeScript and `ts-node`
    *   Configure `tsconfig.json`
4.  [Step 2: Defining Core Types](#step2-core-types)
    *   The `Task` Interface
    *   The `TaskStatus` Enum
5.  [Step 3: Creating the Task Service](#step3-task-service)
    *   `TaskManager` Class
    *   Adding a Task
    *   Listing Tasks
    *   Updating Task Status
    *   Removing a Task
6.  [Step 4: Building the Command-Line Interface](#step4-cli)
    *   Parsing Command-Line Arguments
    *   Handling User Commands
7.  [Step 5: Putting It All Together (`index.ts`)](#step5-index-ts)
8.  [Step 6: Running the Application](#step6-running-app)
9.  [Key TypeScript Concepts Used](#key-concepts)
10. [Further Enhancements](#further-enhancements)
11. [Conclusion](#conclusion)

---

## 1. Introduction & Project Goals <a name="intro-project-goals"></a>
We will create a CLI application that allows users to:
*   Add new tasks.
*   View all tasks.
*   Mark tasks as completed.
*   Remove tasks.

This project will reinforce your knowledge of interfaces, enums, classes, functions, modules, and basic project setup with TypeScript.

## 2. Prerequisites <a name="prerequisites"></a>
*   Node.js and npm installed.
*   Basic understanding of the command line.
*   Familiarity with core TypeScript concepts (covered in earlier tutorials).

## 3. Step 1: Project Setup <a name="step1-project-setup"></a>

Create a new directory for your project, e.g., `simple-task-manager`.
```bash
mkdir simple-task-manager
cd simple-task-manager
```

**Initialize npm:**
This creates a `package.json` file.
```bash
npm init -y
```

**Install TypeScript and `ts-node`:**
*   `typescript`: The TypeScript compiler.
*   `ts-node`: Allows you to run TypeScript files directly without pre-compiling to JavaScript (great for development).
*   `@types/node`: Provides type definitions for Node.js built-in modules.
```bash
npm install typescript ts-node @types/node --save-dev
```

**Configure `tsconfig.json`:**
Create a `tsconfig.json` file in your project root. You can generate a basic one using:
```bash
npx tsc --init
```
For this project, ensure your `tsconfig.json` includes at least:
```json
{
  "compilerOptions": {
    "target": "es6",            // Or newer, like "es2020"
    "module": "commonjs",
    "outDir": "./dist",         // Where compiled JS files will go
    "rootDir": "./src",        // Where your TS source files will be
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],    // Which files to include
  "exclude": ["node_modules"]
}
```
Create a `src` directory for your TypeScript files: `mkdir src`.

## 4. Step 2: Defining Core Types <a name="step2-core-types"></a>
Inside the `src` directory, create `types.ts`.

**`src/types.ts`**
```typescript
export interface Task {
  id: number;
  name: string;
  status: TaskStatus;
  createdAt: Date;
}

export enum TaskStatus {
  Pending = "Pending",
  InProgress = "In Progress",
  Completed = "Completed",
  Cancelled = "Cancelled"
}
```

## 5. Step 3: Creating the Task Service <a name="step3-task-service"></a>
This service will handle the core logic of managing tasks.

Create `src/taskManager.ts`.

**`src/taskManager.ts`**
```typescript
import { Task, TaskStatus } from './types';

export class TaskManager {
  private tasks: Task[] = [];
  private nextId: number = 1;

  addTask(name: string): Task {
    const newTask: Task = {
      id: this.nextId++,
      name,
      status: TaskStatus.Pending,
      createdAt: new Date(),
    };
    this.tasks.push(newTask);
    console.log(`Task "${name}" added with ID ${newTask.id}.`);
    return newTask;
  }

  listTasks(): void {
    if (this.tasks.length === 0) {
      console.log("No tasks available.");
      return;
    }
    console.log("\n--- Your Tasks ---");
    this.tasks.forEach(task => {
      console.log(
        `ID: ${task.id} | Name: ${task.name} | Status: ${task.status} | Created: ${task.createdAt.toLocaleDateString()}`
      );
    });
    console.log("------------------\n");
  }

  updateTaskStatus(id: number, status: TaskStatus): void {
    const task = this.tasks.find(t => t.id === id);
    if (task) {
      task.status = status;
      console.log(`Task ID ${id} status updated to "${status}".`);
    } else {
      console.log(`Error: Task with ID ${id} not found.`);
    }
  }

  removeTask(id: number): void {
    const initialLength = this.tasks.length;
    this.tasks = this.tasks.filter(t => t.id !== id);
    if (this.tasks.length < initialLength) {
      console.log(`Task ID ${id} removed.`);
    } else {
      console.log(`Error: Task with ID ${id} not found for removal.`);
    }
  }
}
```

## 6. Step 4: Building the Command-Line Interface <a name="step4-cli"></a>
We'll use `process.argv` to get command-line arguments. Create `src/cli.ts`.

**`src/cli.ts`**
```typescript
import { TaskManager } from './taskManager';
import { TaskStatus } from './types';

export class CLI {
  private taskManager = new TaskManager();

  constructor() {}

  public processCommands(args: string[]): void {
    const command = args[0];
    const params = args.slice(1);

    switch (command) {
      case 'add':
        if (params.length < 1) {
          console.log("Usage: add <task_name>");
          break;
        }
        this.taskManager.addTask(params.join(' '));
        break;
      case 'list':
        this.taskManager.listTasks();
        break;
      case 'complete':
        if (params.length < 1 || isNaN(parseInt(params[0]))) {
          console.log("Usage: complete <task_id>");
          break;
        }
        this.taskManager.updateTaskStatus(parseInt(params[0]), TaskStatus.Completed);
        break;
      case 'remove':
        if (params.length < 1 || isNaN(parseInt(params[0]))) {
          console.log("Usage: remove <task_id>");
          break;
        }
        this.taskManager.removeTask(parseInt(params[0]));
        break;
      case 'help':
      default:
        this.showHelp();
        break;
    }
  }

  private showHelp(): void {
    console.log(`
Available Commands:
  add <task_name>        - Adds a new task
  list                   - Lists all tasks
  complete <task_id>     - Marks a task as completed
  remove <task_id>       - Removes a task
  help                   - Shows this help message
    `);
  }
}
```

## 7. Step 5: Putting It All Together (`index.ts`) <a name="step5-index-ts"></a>
Create `src/index.ts` as the entry point for our application.

**`src/index.ts`**
```typescript
import { CLI } from './cli';

const args = process.argv.slice(2); // Remove 'node' and script path
const cliApp = new CLI();

if (args.length === 0) {
  cliApp.processCommands(['help']);
} else {
  cliApp.processCommands(args);
}
```

## 8. Step 6: Running the Application <a name="step6-running-app"></a>
Add a script to your `package.json` to easily run the app:
```json
// package.json (inside "scripts")
"scripts": {
  "start": "ts-node src/index.ts",
  "build": "tsc",
  "serve": "node dist/index.js" // After running build
  // ... other scripts
},
```

Now you can run commands:
```bash
npm start add "Buy groceries"
npm start add "Read TypeScript book chapter 5"
npm start list
npm start complete 1
npm start list
npm start remove 2
npm start list
npm start help
```

To build the JavaScript version:
```bash
npm run build
```
And then run the compiled JavaScript:
```bash
npm run serve add "Test compiled version"
npm run serve list
```

## 9. Key TypeScript Concepts Used <a name="key-concepts"></a>
*   **Interfaces (`Task`)**: Defining shapes for our data.
*   **Enums (`TaskStatus`)**: Representing a set of named constants for task states.
*   **Classes (`TaskManager`, `CLI`)**: Organizing code and managing state.
*   **Modules**: Separating concerns into different files (`types.ts`, `taskManager.ts`, `cli.ts`, `index.ts`).
*   **Type Annotations**: For variables, function parameters, and return types.
*   **`tsconfig.json`**: Configuring the TypeScript compiler.
*   **`ts-node`**: For development-time execution.
*   **`@types/node`**: For Node.js specific types.

## 10. Further Enhancements <a name="further-enhancements"></a>
*   **Data Persistence**: Save tasks to a file (JSON) so they persist between runs.
*   **More Task Properties**: Add due dates, priorities, tags, etc.
*   **Interactive Mode**: Instead of one-off commands, create a loop that continuously prompts the user for actions.
*   **Advanced CLI Libraries**: Use libraries like `inquirer` or `commander` for a more sophisticated CLI experience.
*   **Filtering and Sorting**: Allow users to filter tasks by status or sort them.
*   **Unit Tests**: Write tests for the `TaskManager` class.

## 11. Conclusion <a name="conclusion"></a>
Congratulations! You've built a functional Task Manager CLI application using TypeScript. This project demonstrates how TypeScript's features can be used to create well-structured, maintainable, and type-safe applications. We hope this practical exercise helps you feel more confident in applying TypeScript to your own projects.

---

⬅️ [Back to Advanced TypeScript Patterns and Best Practices](../49-ts-advanced-patterns-practices/README.md) | 🏠 [Back to TypeScript Series Home](../README.md)
