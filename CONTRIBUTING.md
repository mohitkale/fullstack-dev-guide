# 🤝 Contributing to MentorHub

First off, thank you for considering contributing to MentorHub! We're excited to have you on board. This guide will help you get started with contributing to our project.

## 📋 Table of Contents
1. [Code of Conduct](#-code-of-conduct)
2. [Getting Started](#-getting-started)
3. [Development Workflow](#-development-workflow)
4. [Code Style](#-code-style)
5. [Commit Message Guidelines](#-commit-message-guidelines)
6. [Pull Request Process](#-pull-request-process)
7. [Reporting Issues](#-reporting-issues)
8. [Feature Requests](#-feature-requests)
9. [Documentation](#-documentation)
10. [Community](#-community)

## ✨ Code of Conduct

This project and everyone participating in it is governed by our [Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code. Please report any unacceptable behavior to [your-email@example.com].

## 🚀 Getting Started

### Prerequisites

- Node.js (v16 or later)
- npm (v7 or later) or yarn
- Git
- A code editor (we recommend [VS Code](https://code.visualstudio.com/))

### Setting Up the Project

1. **Fork the Repository**
   Click the "Fork" button at the top-right of the [MentorHub repository](https://github.com/yourusername/mentorhub).

2. **Clone Your Fork**
   ```bash
   git clone https://github.com/your-username/mentorhub.git
   cd mentorhub
   ```

3. **Add Upstream Remote**
   ```bash
   git remote add upstream https://github.com/yourusername/mentorhub.git
   ```

4. **Install Dependencies**
   ```bash
   # Install root dependencies
   npm install
   
   # Install frontend dependencies
   cd frontend
   npm install
   
   # Install backend dependencies (choose one)
   cd ../backend-postgres  # or backend-mongo or backend-supabase
   npm install
   ```

5. **Set Up Environment Variables**
   ```bash
   # Copy the example environment files
   cp .env.example .env
   # Then edit the .env file with your configuration
   ```

## 🔄 Development Workflow

1. **Sync with Upstream**
   ```bash
   git checkout main
   git fetch upstream
   git merge upstream/main
   ```

2. **Create a New Branch**
   ```bash
   git checkout -b feature/your-feature-name
   # or
   git checkout -b fix/your-bug-fix
   ```

3. **Make Your Changes**
   - Follow the code style guidelines below
   - Write tests for your changes
   - Update documentation as needed

4. **Run Tests**
   ```bash
   # Run all tests
   npm test
   
   # Run specific test file
   npm test -- path/to/test/file.test.tsx
   ```

5. **Lint Your Code**
   ```bash
   npm run lint
   ```

6. **Commit Your Changes**
   ```bash
   git add .
   git commit -m "type(scope): your commit message"
   ```

7. **Push to Your Fork**
   ```bash
   git push origin your-branch-name
   ```

8. **Create a Pull Request**
   - Go to the [MentorHub repository](https://github.com/yourusername/mentorhub)
   - Click "New Pull Request"
   - Select your branch
   - Fill in the PR template
   - Submit the PR

## 🎨 Code Style

### General Guidelines
- Use TypeScript for all new code
- Follow the Airbnb JavaScript Style Guide
- Use ESLint and Prettier for code formatting
- Write meaningful variable and function names
- Keep functions small and focused on a single task

### React/React Native Specific
- Use functional components with hooks
- Prefer TypeScript interfaces over types
- Use prop-types for prop validation
- Keep components small and reusable
- Use meaningful component and file names (PascalCase for components)

### File Structure
- Keep related files together (component + styles + tests)
- Use index.ts files for cleaner imports
- Group related components in feature folders

## 📝 Commit Message Guidelines

We follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
type(scope): subject

[optional body]

[optional footer]
```

### Types
- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation changes
- **style**: Code style changes (formatting, missing semicolons, etc.)
- **refactor**: Code changes that neither fix a bug nor add a feature
- **perf**: Code changes that improve performance
- **test**: Adding or updating tests
- **chore**: Changes to the build process or auxiliary tools

### Examples
```
feat(auth): add login with Google

- Add Google OAuth integration
- Update login page with Google button

Closes #123
```

## 🔍 Pull Request Process

1. Ensure any install or build dependencies are removed before the end of the layer when doing a build.
2. Update the README.md with details of changes to the interface, including new environment variables, exposed ports, useful file locations, and container parameters.
3. Increase the version numbers in any example files and the README.md to the new version that this Pull Request would represent. The versioning scheme we use is [SemVer](http://semver.org/).
4. Your pull request will be reviewed by the maintainers. We may suggest changes or improvements.
5. Once approved, your PR will be merged into the main branch.

## 🐛 Reporting Issues

Before submitting an issue, please check the following:

1. Search the [existing issues](https://github.com/yourusername/mentorhub/issues) to see if the problem has already been reported.
2. Make sure you're using the latest version of the code.

### How to Report an Issue

1. Use the issue template provided when creating a new issue.
2. Include a clear title and description.
3. Provide steps to reproduce the issue.
4. Include any relevant error messages or screenshots.
5. Specify your environment (OS, browser, Node.js version, etc.).

## 💡 Feature Requests

We welcome feature requests! Before submitting a new feature request, please:

1. Check if a similar feature has already been requested.
2. Clearly describe the feature you'd like to see.
3. Explain why this feature would be useful.
4. Include any relevant examples or mockups.

## 📚 Documentation

Good documentation is crucial for any open-source project. If you're adding new features or making changes to existing ones, please update the relevant documentation.

### Updating Documentation

1. Documentation is written in Markdown.
2. Keep lines under 100 characters.
3. Use proper heading hierarchy.
4. Add code examples where appropriate.
5. Update the table of contents if needed.

## 🌟 Your First Contribution

Looking for your first contribution? Here are some good places to start:

- Look for issues labeled "good first issue" or "help wanted"
- Fix typos in the documentation
- Add test coverage
- Improve error messages
- Write tutorials or examples

## 🤝 Community

Join our community to ask questions and discuss MentorHub:

- [Discord/Slack Channel](#) (link to be added)
- [GitHub Discussions](https://github.com/yourusername/mentorhub/discussions)
- [Twitter](#) (link to be added)

## 🙏 Thank You

Your contributions to open source, large or small, make great projects like this possible. Thank you for taking the time to contribute!
