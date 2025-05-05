# 🧪 Application Stack for Dev / Students – Hybrid University

This document outlines the standard internal application stack used by students, developers, and project teams for software development, testing, and prototyping within Hybrid University. The stack is designed to be modern, scalable, and deployable in both local and cloud environments.

---

## 🧱 Stack Overview

| Layer      | Stack                                    | Notes                                         |
|------------|------------------------------------------|-----------------------------------------------|
| **Frontend** | React.js + TailwindCSS                  | Responsive, component-based UI framework      |
| **Backend**  | Django (Python 3.11) / Node.js (v20)    | REST APIs, MVC pattern, and scalable logic    |
| **Database** | PostgreSQL 15 (AWS RDS or Local)        | Advanced open-source SQL database             |
| **Container**| Docker + Docker Compose                 | Isolated development & deployment environments|
| **CI/CD**    | GitHub Actions or Jenkins               | Automated builds, tests, and deployment       |

---

## 💻 Frontend Stack

- **React.js** for component-driven development.
- **TailwindCSS** used for utility-first responsive design.
- Deployed via:
  - Static site hosting (e.g., Netlify, GitHub Pages)
  - Dockerized container for full-stack deployment

### 📦 Example Dev Environment Setup

```bash
npx create-react-app my-app
cd my-app
npm install tailwindcss

```

## 🛠️ Backend Stack Options

## 🐍 Django (Python 3.11)
RESTful APIs built using Django REST Framework.
Integrated with PostgreSQL.
Authentication via JWT or OAuth2.

## 🔁 Node.js (v20)
Express.js framework for rapid API development.
Uses Sequelize ORM for PostgreSQL or MongoDB if needed.
Ideal for microservices and lightweight tasks.

## 🗃️ Database – PostgreSQL 15

Default database for academic and internal apps.
Hosted on:
AWS RDS (for cloud-native apps)
Local Docker containers for dev/testing
DB access managed via pgAdmin and SSL.

### 🐳 Docker Example

```bash
yaml
Copy
Edit
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: devuser
      POSTGRES_PASSWORD: devpass
      POSTGRES_DB: devdb
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:

```

## 📦 Containerization – Docker + Compose

All dev environments are standardized using Docker Compose.
Frontend, backend, and DB services are isolated but networked.
Easy onboarding for student teams.

### 🧪 Sample docker-compose.yml

```bash
yaml
Copy
Edit
version: "3"
services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
```

## 🔁 CI/CD with GitHub Actions or Jenkins

GitHub Actions for lightweight projects:
Linting, unit testing, container build, and deploy.
Jenkins used for advanced pipelines:
Integration with Docker Hub, AWS CLI, and Kubernetes.

### 🧪 Sample GitHub Workflow

```bash
yaml
Copy
Edit
name: CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'
      - run: npm install
      - run: npm test

```

## 🧩 Use Cases

Use Case	Description
Student Projects	Final year and team-based academic projects
Research Prototypes	Testbed for machine learning and NLP experiments
Dev/Test Environment	Mirror production-like environments for testing
Web App Internships	Interns work on internal tools using this stack

## 🔐 Access & Security Practices

Developer permissions managed via GitHub EDU teams or Gitea groups.
API keys and secrets stored in .env files (not committed to Git).
Role-based access enforced at the application level.
All Docker containers run as non-root where possible.

## 📌 Summary

The developer stack at Hybrid University empowers student and faculty teams to quickly build, test, and deploy full-stack applications. Using a modern frontend, scalable backend frameworks, and PostgreSQL, this stack forms the backbone for rapid prototyping and research-driven innovation.
