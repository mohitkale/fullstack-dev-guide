# 🧠 MentorHub: Full Stack Learning Hub for Freshers

Welcome to the **MentorHub** project — a self-paced, structured learning repository designed for 1000s of beginners and mentees to master full-stack development by **building a real-world app**, step-by-step with detailed, nested README guides.

---

## 🚀 Objective

To help freshers and beginners learn full stack development using real use-cases with practical implementation by following clean, beginner-friendly README files.

This project aims to mimic a W3Schools-style experience — but focused only on real-world *implementation* and *essentials*, not everything theoretical.

---

## 📚 Real-World Project Theme: “WeAll” (Social Media App)

Every README and learning guide will revolve around building the **WeAll app** (a social media app like Instagram) using different stacks for backend, a unified React web frontend, and a mobile app with React Native.

### Core Features Covered:

1. **Authentication**
   - User registration, login, logout
   - Forgot/reset password
   - Update/delete profile

2. **Feed System**
   - Create post (text, images, videos, links)
   - Infinite scroll
   - Likes, comments, shares, post analytics (views)
   - Comment replies

3. **Follow System**
   - Follow/unfollow users
   - View followers/following list
   - Prioritized feed based on followed users

4. **Moderation**
   - Report post/comment
   - Block user (filters blocked content from feed)

---

## 🏗️ Project Structure Overview

mentorhub/
├── README.md                    ← Overview and getting started
├── backend-postgres/README.md  ← Backend with PostgreSQL
├── backend-mongo/README.md     ← Backend with MongoDB
├── backend-supabase/README.md  ← Backend with Supabase
├── frontend/README.md          ← Web app (React + Vite)
├── mobile/README.md            ← Mobile app (React Native)
└── docs/
    ├── auth/README.md          ← Guides for auth features
    ├── post/README.md          ← Guides for feed-related features
    ├── follow/README.md        ← Guides for follow system
    └── moderation/README.md    ← Guides for report/block features


Each folder contains:
- A README for that tech stack
- Real working feature guides
- Code snippets + explanations
- Focused, beginner-friendly tone

---

## ⚙️ Tech Stack

### 🖥 Frontend (Web)
- React + Vite (TypeScript)
- **Custom CSS only** (No Tailwind/Bootstrap)

### 📱 Mobile
- React Native with Expo (TSX)
- **Custom styling only**

### 🔐 Backend (3 separate implementations)
1. **ExpressJS + PostgreSQL**
   - NeonDB + Prisma or pg
2. **ExpressJS + MongoDB**
   - Mongoose ODM
3. **Supabase (Client SDK)**
   - BaaS API with Row-level security

---

## 📌 PRD Goals for AI Tools (Windsurf IDE, GPT agents, etc.)

You are an autonomous AI coding assistant.
Use this markdown PRD to generate **multiple README files**, one per feature per stack.

Each README must:
- Be beginner-focused (explain like I’m new to coding)
- Have full code snippets with explanation
- Reference files/folders
- Include terminal commands, screenshots (if needed)
- Avoid jargon and avoid UI frameworks
- Incrementally build toward full-featured app

---

## ✅ Prompt Template for AI Tools

Use this prompt format for each README:

> You are an expert AI developer and teacher. Generate a beginner-friendly `README.md` to explain how to implement **[FEATURE]** in **[STACK]** as part of the **\"WeAll\"** social media app. Use step-by-step instructions, real file references, CLI commands, and clean code snippets. Avoid using UI libraries. Explain concepts in plain English so even non-coders can follow.

---

## 📘 Example Feature Breakdown

### 1. Authentication (docs/auth/README.md)
- Signup, Login, Forgot Password
- JWT setup
- Token-based access middleware
- Password reset flow

### 2. Feed System (docs/post/README.md)
- Create Post (text, image upload, video link)
- Like/Comment system
- Share post
- View count logic
- Infinite scroll (pagination)
- Feed filter (exclude blocked, prioritize followed)

### 3. Follow System (docs/follow/README.md)
- Follow/unfollow APIs
- Followers/following lists
- Feed prioritization logic

### 4. Moderation System (docs/moderation/README.md)
- Report post/comment
- Block user
- Admin/Moderator considerations

---

## 🧩 Order of Execution for AI README Generation

1. Generate **project root README.md**
2. Then in this order:
   - backend-postgres/README.md
   - backend-mongo/README.md
   - backend-supabase/README.md
   - frontend/README.md
   - mobile/README.md
   - docs/auth/README.md
   - docs/post/README.md
   - docs/follow/README.md
   - docs/moderation/README.md

Each README should independently guide the user on how to implement only that part of the app using one technology stack. You may reuse logic across stacks but ensure separation and clarity for learning purposes.

---

## 📍 Final Notes

- No UI libraries
- No overwhelming theory
- Only practical, real-world steps to implement the WeAll app
- Each README should build directly toward a working feature
- All examples should assume **zero coding knowledge**

---

> Let’s begin with the root `README.md`. Once done, proceed to backend-postgres and so on.