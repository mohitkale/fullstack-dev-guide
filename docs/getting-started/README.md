# 🚀 Getting Started with MentorHub

Welcome to your journey of becoming a full-stack developer! This guide will help you set up your development environment and get started with the MentorHub project.

## 📋 Table of Contents
1. [What is MentorHub?](#-what-is-mentorhub)
2. [Learning Approach](#-learning-approach)
3. [Prerequisites](#-prerequisites)
4. [Development Environment Setup](#-development-environment-setup)
   - [Windows](#windows)
   - [macOS](#macos)
   - [Linux](#linux)
5. [Project Structure](#-project-structure)
6. [Next Steps](#-next-steps)

## 🤔 What is MentorHub?

MentorHub is a comprehensive learning platform where you'll build a real-world social media application called "WeAll" while learning full-stack development. You'll work with modern technologies and best practices used in the industry.

## 🎯 Learning Approach

We believe in learning by doing. Here's how we'll approach this journey:

1. **Learn by Building**: You'll build a real application from scratch
2. **Step-by-Step**: Each concept builds on the previous one
3. **Multiple Backends**: Learn different database technologies
4. **Real-world Practices**: Follow industry standards and best practices
5. **Hands-on Exercises**: Practice what you learn with guided exercises

## 🖥️ Prerequisites

Before we begin, make sure you have:

- A computer with at least 4GB RAM (8GB recommended)
- Basic understanding of JavaScript/TypeScript
- Basic understanding of HTML and CSS
- A code editor (we recommend [VS Code](https://code.visualstudio.com/))
- Git installed on your system

## ⚙️ Development Environment Setup

### Windows

1. **Install Node.js**
   - Download the LTS version from [nodejs.org](https://nodejs.org/)
   - Run the installer and follow the instructions
   - Verify installation:
     ```bash
     node --version
     npm --version
     ```

2. **Install Git**
   - Download Git from [git-scm.com](https://git-scm.com/download/win)
   - Run the installer with default options
   - Verify installation:
     ```bash
     git --version
     ```

### macOS

1. **Install Homebrew (package manager)**
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

2. **Install Node.js**
   ```bash
   brew install node
   ```
   Verify installation:
   ```bash
   node --version
   npm --version
   ```

3. **Install Git**
   ```bash
   brew install git
   ```
   Verify installation:
   ```bash
   git --version
   ```

### Linux (Ubuntu/Debian)

1. **Update package list**
   ```bash
   sudo apt update
   ```

2. **Install Node.js and npm**
   ```bash
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt install -y nodejs
   ```
   Verify installation:
   ```bash
   node --version
   npm --version
   ```

3. **Install Git**
   ```bash
   sudo apt install git
   ```
   Verify installation:
   ```bash
   git --version
   ```

## 📁 Project Structure

Here's an overview of the project structure:

```
mentorhub/
├── backend-postgres/    # Express + PostgreSQL backend
├── backend-mongo/       # Express + MongoDB backend
├── backend-supabase/    # Supabase backend
├── frontend/            # React web frontend
├── mobile/              # React Native mobile app
└── docs/                # Documentation
    ├── getting-started/ # You are here
    ├── frontend/       # Frontend guides
    ├── backend/        # Backend guides
    └── mobile/        # Mobile development guides
```

## 🚦 Next Steps

1. [Set up your development environment](./setup-environment.md)
2. [Learn about version control with Git](./version-control.md)
3. [Start with the frontend development guide](../frontend/README.md)

## ❓ Need Help?

If you get stuck at any point:

1. Check the [FAQ](./faq.md)
2. Search for your issue in the [GitHub Issues](https://github.com/yourusername/mentorhub/issues)
3. If you can't find a solution, open a new issue with details about your problem

Happy coding! 🎉
