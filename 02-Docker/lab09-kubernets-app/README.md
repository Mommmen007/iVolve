# 🐳 Lab 09: Containerized Node.js and MySQL Stack Using Docker Compose

## 📌 Overview

This lab demonstrates how to deploy a multi-container application using **Docker Compose**.

The project consists of:

- A **Node.js** web application
- A **MySQL 8** database
- A persistent Docker Volume
- Automatic networking between containers

Instead of running multiple `docker run` commands manually, Docker Compose orchestrates the entire application stack using a single configuration file.

---

## 🎯 Objectives

- Build the Node.js application image from the local Dockerfile
- Deploy a MySQL database container
- Configure environment variables for database connectivity
- Create persistent storage using Docker Volumes
- Verify application health endpoints
- Push the custom Docker image to Docker Hub

---

## 📂 Project Structure

```text
lab09-kubernets-app/
│
├── Dockerfile
├── docker-compose.yml
├── package.json
├── server.js
├── db.js
└── frontend/
    ├── index.html
    └── assets/
```

---

## 🏗️ Architecture

```text
                Docker Compose
                       │
        ┌──────────────┴──────────────┐
        │                             │
        ▼                             ▼
   Node.js App                  MySQL Database
        │                             │
        └──────────────┬──────────────┘
                       │
                Docker Network
                       │
                 Docker Volume
                 (db_data)
```

---

## ⚙️ Technologies Used

- Docker
- Docker Compose
- Node.js
- Express.js
- MySQL 8
- Alpine Linux

---

## 📄 Docker Compose Configuration

The stack contains two services:

### App Service

- Builds the image from the local Dockerfile
- Exposes port **3000**
- Uses environment variables:
  - DB_HOST
  - DB_USER
  - DB_PASSWORD
- Depends on the MySQL service

### Database Service

- Uses the official MySQL 8 image
- Creates the **ivolve** database
- Configures the root password
- Stores data in a persistent Docker Volume

---

## 🚀 Running the Project

Build and start the application:

```bash
docker compose up --build
```

Stop the containers:

```bash
docker compose down
```

Remove containers and volumes:

```bash
docker compose down -v
```

---

## ✅ Verification

Application:

```
http://localhost:3000
```

Health Check:

```
http://localhost:3000/health
```

Readiness Check:

```
http://localhost:3000/ready
```

Application Logs:

```bash
docker compose logs app
```

---

## 📸 Screenshots

### Docker Compose Logs

<img width="1447" height="849" alt="image" src="https://github.com/user-attachments/assets/2e13e8e5-7dea-4a6f-aeb9-96abf6ba66d8" />


Example:

```
Connected to MySQL and 'ivolve' DB found.
Server started on http://0.0.0.0:3000
```

### Application Running

<img width="730" height="465" alt="4Capture" src="https://github.com/user-attachments/assets/86096668-179b-49eb-9e14-7eb913f5b12c" />


```
http://localhost:3000
```

---

## 📚 What I Learned

- Building multi-container applications
- Docker Compose fundamentals
- Service-to-service communication
- Docker networking
- Persistent storage with Docker Volumes
- Environment variable management
- Container orchestration basics

---

## 📖 Lab Requirements

This lab covers:

- Building a Node.js image from a Dockerfile
- Deploying MySQL using Docker Compose
- Creating the **ivolve** database
- Verifying application health endpoints
- Viewing application logs
- Publishing the image to Docker Hub

---

## 👨‍💻 Author

**Momen Elgedawy**

Cloud & DevOps Engineer

GitHub:
https://github.com/Mommmen007
