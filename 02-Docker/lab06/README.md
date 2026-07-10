# Lab 06 - Managing Docker Environment Variables Across Build and Runtime

## 📌 Overview
This lab demonstrates different ways to manage **environment variables** in Docker during both **build time** and **runtime**.

The application is a simple **Flask** web application running inside a Docker container.

---

## 🎯 Objectives

- Build a Docker image for a Python Flask application.
- Use environment variables in different ways:
  - Runtime variables using `docker run -e`
  - Runtime variables using `--env-file`
  - Default variables using `ENV` in the Dockerfile
- Expose the application on port **8080**.

---

## 🛠️ Tech Stack

- Docker
- Python 3.11
- Flask

---

## 📂 Project Structure

```text
lab06/
├── app.py
├── Dockerfile
├── staging.env
├── README.md
└── .gitignore
```

---

## 🐳 Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY . .

RUN pip install flask

ENV APP_MODE=production
ENV APP_REGION=canada-west

EXPOSE 8080

CMD ["python", "app.py"]
```

---

## 🚀 Build Docker Image

```bash
docker build -t lab06app .
```

---

# Running Containers

## 1️⃣ Development Environment

Environment variables are passed directly in the `docker run` command.

```bash
docker run -d \
--name app-dev \
-p 8081:8080 \
-e APP_MODE=development \
-e APP_REGION=us-east \
lab06app
```

---

## 2️⃣ Staging Environment

Create a file named `staging.env`

```text
APP_MODE=staging
APP_REGION=us-west
```

Run the container:

```bash
docker run -d \
--name app-stage \
-p 8082:8080 \
--env-file staging.env \
lab06app
```

---

## 3️⃣ Production Environment

The production values are already defined inside the Dockerfile using the `ENV` instruction.

```bash
docker run -d \
--name app-prod \
-p 8083:8080 \
lab06app
```

---

# Verify Environment Variables

Check the environment variables inside a running container.

```bash
docker exec app-dev printenv APP_MODE
docker exec app-dev printenv APP_REGION
```

or

```bash
docker exec app-stage env
```

---

# Docker Images

```bash
docker images
```

---

# Running Containers

```bash
docker ps
```

---

## 📚 Key Concepts Learned

- Docker `ENV`
- Runtime Environment Variables
- `docker run -e`
- `--env-file`
- Default vs Override Environment Variables
- Docker Build Process
- Flask Containerization

---

## 💡 Environment Variables Priority

Docker resolves environment variables using the following priority:

1. `docker run -e`
2. `--env-file`
3. `ENV` in Dockerfile (default values)

---

## 📖 What I Learned

In this lab, I learned how to configure Docker containers using environment variables in multiple ways. I also understood the difference between defining default values inside the Docker image and overriding them at runtime, which is a common practice when deploying applications across different environments such as Development, Staging, and Production.

---

## 👨‍💻 Author

**Momen Elgedawy**

Cloud & DevOps Engineer
