# 🚀 Getting Started with MentorHub

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://github.com/yourusername/mentorhub/codespaces)
[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/yourusername/mentorhub)

Welcome to your journey of becoming a full-stack developer! This guide will help you set up your development environment and get started with the MentorHub project.

> **Note**: If you prefer not to set up the environment locally, you can use one of the cloud development environments above to get started instantly.

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

Before we begin, make sure you have the following installed on your system:

### Hardware Requirements
- Computer with at least 4GB RAM (8GB recommended)
- At least 2GB of free disk space

### Software Requirements
- **Node.js** (v18 or later) - [Download](https://nodejs.org/)
- **npm** (comes with Node.js) or **Yarn** (v1.22+) - [Yarn Installation](https://classic.yarnpkg.com/en/docs/install/)
- **Git** - [Download](https://git-scm.com/)
- **Code Editor** - We recommend [VS Code](https://code.visualstudio.com/)

### Recommended VS Code Extensions
- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
- [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
- [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.volar)
- [REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)

### Knowledge Prerequisites
- Basic understanding of JavaScript/TypeScript
- Familiarity with HTML and CSS
- Basic Git knowledge (clone, commit, push, pull)

## ⚙️ Development Environment Setup

### Windows

1. **Install Node.js**
   - Download the LTS version from [nodejs.org](https://nodejs.org/)
   - Run the installer and follow the instructions
   - Verify installation by opening Command Prompt and running:
     ```bash
     node --version
     npm --version
     ```
   - (Optional) Install Yarn globally:
     ```bash
     npm install -g yarn
     yarn --version
     ```

2. **Install Git**
   - Download Git from [git-scm.com](https://git-scm.com/download/win)
   - Run the installer with these recommended options:
     - Select "Use Git from Git Bash and also from 3rd-party software"
     - Choose "Checkout as-is, commit as-is" for line endings
     - Select "Use Windows' default console window"
     - Choose "Default (fast-forward or merge)" for pull behavior
   - Verify installation:
     ```bash
     git --version
     git config --global user.name "Your Name"
     git config --global user.email "your.email@example.com"
     ```

3. **Set Up SSH Keys (Recommended)**
   - Open Git Bash and run:
     ```bash
     ssh-keygen -t ed25519 -C "your.email@example.com"
     ```
   - Press Enter to accept the default file location
   - Enter a secure passphrase when prompted
   - Start the SSH agent:
     ```bash
     eval "$(ssh-agent -s)"
     ssh-add ~/.ssh/id_ed25519
     ```
   - Copy your public key to clipboard:
     ```bash
     cat ~/.ssh/id_ed25519.pub | clip
     ```
   - Add the SSH key to your GitHub account:
     - Go to GitHub → Settings → SSH and GPG keys → New SSH key
     - Paste your key and save

### macOS

1. **Install Xcode Command Line Tools**
   ```bash
   xcode-select --install
   ```
   - Click "Install" when prompted to install the command line developer tools

2. **Install Homebrew (Package Manager)**
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
   - Follow the on-screen instructions to add Homebrew to your PATH
   - Verify installation:
     ```bash
     brew --version
     ```

3. **Install Node.js**
   ```bash
   # Install Node.js using Homebrew
   brew install node
   
   # Verify installation
   node --version
   npm --version
   
   # (Optional) Install Yarn
   npm install -g yarn
   yarn --version
   ```

4. **Install Git**
   ```bash
   # Install Git using Homebrew
   brew install git
   
   # Verify installation
   git --version
   
   # Configure Git
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"
   ```

5. **Set Up SSH Keys**
   ```bash
   # Generate SSH key
   ssh-keygen -t ed25519 -C "your.email@example.com"
   
   # Add SSH key to the SSH agent
   eval "$(ssh-agent -s)"
   ssh-add --apple-use-keychain ~/.ssh/id_ed25519
   
   # Copy the public key to clipboard
   pbcopy < ~/.ssh/id_ed25519.pub
   
   # Add the SSH key to your GitHub account
   # Go to GitHub → Settings → SSH and GPG keys → New SSH key
   # Paste your key and save
   ```

### Linux (Ubuntu/Debian)

1. **Update Package List**
   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

2. **Install Node.js and npm**
   ```bash
   # Using NodeSource (recommended)
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt-get install -y nodejs
   
   # Verify installation
   node --version
   npm --version
   
   # (Optional) Install Yarn
   curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
   echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
   sudo apt update
   sudo apt install --no-install-recommends yarn
   yarn --version
   ```

3. **Install Git**
   ```bash
   sudo apt install git
   
   # Verify installation
   git --version
   
   # Configure Git
   git config --global user.name "Your Name"
   git config --global user.email "your.email@example.com"
   ```

4. **Set Up SSH Keys**
   ```bash
   # Generate SSH key
   ssh-keygen -t ed25519 -C "your.email@example.com"
   
   # Start the SSH agent
   eval "$(ssh-agent -s)"
   
   # Add your SSH private key to the agent
   ssh-add ~/.ssh/id_ed25519
   
   # Copy the public key to clipboard
   sudo apt install xclip
   xclip -selection clipboard < ~/.ssh/id_ed25519.pub
   
   # Add the SSH key to your GitHub account
   # Go to GitHub → Settings → SSH and GPG keys → New SSH key
   # Paste your key and save
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

### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/mentorhub.git
cd mentorhub
```

### 2. Install Dependencies
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

### 3. Set Up Environment Variables
```bash
# Frontend
cd frontend
cp .env.example .env
# Edit .env with your configuration

# Backend
cd ../backend-postgres  # or your chosen backend
cp .env.example .env
# Edit .env with your database credentials
```

### 4. Set Up Database
#### For PostgreSQL:
```bash
# Run database migrations
npx prisma migrate dev --name init
```

#### For MongoDB:
- Make sure MongoDB is running locally or update the connection string in `.env`

### 5. Start Development Servers
```bash
# In one terminal
cd backend-postgres  # or your chosen backend
npm run dev

# In another terminal
cd frontend
npm run dev
```

### 6. Explore the Application
- Frontend: http://localhost:5173
- API Documentation: http://localhost:3000/api-docs (if using Swagger/OpenAPI)

## 📚 Learning Resources

- [React Documentation](https://reactjs.org/docs/getting-started.html)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Express.js Guide](https://expressjs.com/en/guide/routing.html)
- [Prisma Documentation](https://www.prisma.io/docs/)
- [MongoDB University](https://university.mongodb.com/)
- [React Native Documentation](https://reactnative.dev/docs/getting-started)

## ❓ Need Help?

If you get stuck at any point:

1. **Check the Documentation**
   - [Frontend Guide](../frontend/README.md)
   - [Backend Guide](../backend/README.md)
   - [Mobile Guide](../mobile/README.md)

2. **Search Existing Issues**
   - [GitHub Issues](https://github.com/yourusername/mentorhub/issues)
   - [GitHub Discussions](https://github.com/yourusername/mentorhub/discussions)

3. **Ask for Help**
   - [Open a new issue](https://github.com/yourusername/mentorhub/issues/new/choose)
   - Join our [Discord/Slack community](#) (link coming soon)
   - Email us at [support@mentorhub.dev](mailto:support@mentorhub.dev)

## 🐛 Found a Bug?

If you find a bug or unexpected behavior, please:

1. Check if the issue has already been reported
2. If not, open a new issue with:
   - A clear title and description
   - Steps to reproduce the issue
   - Expected vs actual behavior
   - Screenshots if applicable
   - Browser/OS version
   - Any error messages

## 💡 Have a Feature Request?

We'd love to hear your ideas! Please:

1. Check if the feature has already been requested
2. Open a new feature request issue with:
   - A clear description of the feature
   - Why this feature would be valuable
   - Any examples or mockups

## 🤝 Contributing

We welcome contributions from the community! Please read our [Contributing Guidelines](../../CONTRIBUTING.md) to get started.

Happy coding! 🎉
