# 🧠 MentorHub: Full Stack Learning Path

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](./CONTRIBUTING.md)
[![Code of Conduct](https://img.shields.io/badge/code%20of-conduct-ff69b4.svg)](./CODE_OF_CONDUCT.md)

Welcome to **MentorHub** - your guided journey to becoming a full-stack developer! This project is designed to help you learn by building a real-world social media application called "WeAll".

## 📺 Demo

[Live Demo](#) (Coming soon)

## 🌟 Features

- **User Authentication**: Secure signup, login, and password reset
- **Social Feed**: Create, like, and comment on posts
- **User Profiles**: Customizable profiles with avatars
- **Real-time Updates**: Live notifications and messaging
- **Responsive Design**: Works on desktop and mobile devices

## 🌟 What You'll Learn

- **Frontend Development**: Build responsive web interfaces with React and TypeScript
- **Backend Development**: Master different database technologies (PostgreSQL, MongoDB, Supabase)
- **Mobile Development**: Create cross-platform mobile apps with React Native
- **Authentication & Security**: Implement secure user authentication and authorization
- **Real-time Features**: Build interactive features like posts, comments, and notifications

## 🛠 Tech Stack

### Frontend (Web)
- React 18 with TypeScript
- Vite for fast development
- Custom CSS (No UI frameworks)

### Mobile
- React Native with Expo
- TypeScript
- Custom styling

### Backend (Choose one)
1. **Express + PostgreSQL** (with Prisma)
2. **Express + MongoDB** (with Mongoose)
3. **Supabase** (Backend as a Service)

## 📚 Learning Path

We'll build the WeAll app step by step through these modules:

1. **Project Setup & Environment**
   - [x] [Getting Started](./docs/getting-started/README.md)
   - [ ] Development Environment Setup
   - [ ] Version Control with Git

2. **Frontend Development**
   - [ ] React Fundamentals
   - [ ] State Management
   - [ ] API Integration
   - [ ] Styling with CSS

3. **Backend Development**
   - [ ] Choose your backend stack
   - [ ] Database Design
   - [ ] API Development
   - [ ] Authentication

4. **Mobile Development**
   - [ ] React Native Basics
   - [ ] Navigation
   - [ ] Device Features

5. **Deployment**
   - [ ] Web Deployment
   - [ ] Mobile App Publishing
   - [ ] CI/CD Pipeline

## 🚀 Quick Start

### Prerequisites

Make sure you have the following installed on your system:

- [Node.js](https://nodejs.org/) (v18 or later)
- [Git](https://git-scm.com/)
- [npm](https://www.npmjs.com/) (comes with Node.js) or [Yarn](https://yarnpkg.com/)
- [VS Code](https://code.visualstudio.com/) (recommended) or your preferred code editor

### Clone the Repository

1. Clone the repository and navigate to the project directory:
   ```bash
   git clone https://github.com/yourusername/mentorhub.git
   cd mentorhub
   ```

2. Install the project dependencies:
   ```bash
   # Install root dependencies
   npm install
   ```

### Backend Setup

Choose one of the following backends to work with:

#### Option 1: PostgreSQL with Prisma
```bash
cd backend-postgres
npm install
cp .env.example .env  # Update with your database credentials
npx prisma migrate dev --name init
```

#### Option 2: MongoDB with Mongoose
```bash
cd backend-mongo
npm install
cp .env.example .env  # Update with your MongoDB URI
```

#### Option 3: Supabase
```bash
cd backend-supabase
npm install
cp .env.example .env  # Update with your Supabase credentials
```

### Frontend Setup

```bash
cd frontend
npm install
cp .env.example .env  # Update with your API endpoints
```

### Start Development Servers

1. Start the backend (from the backend directory):
   ```bash
   npm run dev
   ```

2. In a new terminal, start the frontend:
   ```bash
   cd frontend
   npm run dev
   ```

3. Open [http://localhost:5173](http://localhost:5173) in your browser

## 📚 Documentation

- [Getting Started](./docs/getting-started/README.md)
- [Frontend Development](./docs/frontend/README.md)
- [Backend Development](./docs/backend/README.md)
- [Mobile Development](./docs/mobile/README.md)
- [API Reference](./docs/api/README.md) (Coming soon)
- [Deployment Guide](./docs/deployment/README.md) (Coming soon)

## 🤝 Contributing

We welcome contributions from everyone! Here's how you can help:

1. **Report bugs**: File an issue describing the bug
2. **Suggest features**: Propose new features or improvements
3. **Contribute code**: Submit a pull request

Please read our [Contributing Guidelines](./CONTRIBUTING.md) and [Code of Conduct](./CODE_OF_CONDUCT.md) before getting started.

## 🛠 Built With

- [React](https://reactjs.org/) - Frontend library
- [TypeScript](https://www.typescriptlang.org/) - Type-safe JavaScript
- [Express](https://expressjs.com/) - Backend framework
- [PostgreSQL](https://www.postgresql.org/) / [MongoDB](https://www.mongodb.com/) / [Supabase](https://supabase.io/) - Database options
- [React Native](https://reactnative.dev/) - Mobile app development
- [Vite](https://vitejs.dev/) - Frontend build tool

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

## 🙏 Acknowledgments

- [React Documentation](https://reactjs.org/docs/getting-started.html)
- [TypeScript Documentation](https://www.typescriptlang.org/docs/)
- [Express Documentation](https://expressjs.com/)
- [Prisma Documentation](https://www.prisma.io/docs/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [Supabase Documentation](https://supabase.io/docs/)

## 👥 Contributors

<a href="https://github.com/yourusername/mentorhub/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=yourusername/mentorhub" />
</a>

## 📬 Contact

Have questions or feedback? [Open an issue](https://github.com/yourusername/mentorhub/issues) or reach out to us at [your-email@example.com](mailto:your-email@example.com)
