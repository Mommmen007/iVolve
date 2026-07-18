# Docker Labs - Containerization & Image Optimization

This repository contains my Docker hands-on labs completed during the Cloud DevOps Accelerator Program. These labs cover the fundamentals of Docker, including containerizing applications, optimizing Docker images, and managing environment variables across different deployment environments.

## 📂Repository Structure

```text
.
├── lab03/
├── lab04/
├── lab05/
├── lab06/
└── README.md
```

> If you are using a single Dockerfile, rename it according to the lab before building.

---

## 📚 Labs Included

✅ Lab 3 – Spring Boot Containerization
Containerized a Java Spring Boot application.

Used a Maven base image with Java 17.

Built the application inside the container.

Exposed port 8080.

Ran the generated JAR file inside the container.

✅ Lab 4 – Optimized Spring Boot Image
Built the Spring Boot application outside Docker.

Used a lightweight Java runtime image.

Copied only the generated JAR into the image.

Reduced image size by separating build and runtime.

✅ Lab 5 – Multi-Stage Docker Build
Implemented a multi-stage Docker build.

Used Maven for the build stage.

Used a lightweight Java runtime image for production.

Copied only the compiled JAR from the builder stage.

Built a smaller and more secure Docker image.

✅ Lab 6 – Managing Docker Environment Variables
Containerized a Python Flask application.

Installed Flask inside the Docker image.

Configured environment variables using:

Runtime variables (docker run -e)

Environment file (--env-file)

Default Dockerfile variables (ENV)

Exposed port 8080.

---

# Lab 3 - Build Application Inside Docker

### Objective

Build the Spring Boot application inside the Docker image using Maven.

### Base Image

- Maven
- Java 17

### Steps

- Clone the application
- Copy the source code into the container
- Build the application using Maven
- Run the generated JAR
- Expose port 8080

### Build Image

```bash
docker build -f Dockerfile.lab3 -t app1 .
```

### Run Container

```bash
docker run -d --name container1 -p 8080:8080 app1
```

---

# Lab 4 - Run Pre-built JAR

### Objective

Build the application outside Docker, then create a lightweight image containing only Java Runtime and the generated JAR.

### Build Application

```bash
mvn clean package
```

### Base Image

- Java 17 Runtime

### Build Image

```bash
docker build -f Dockerfile.lab4 -t app2 .
```

### Run Container

```bash
docker run -d --name container2 -p 8080:8080 app2
```

---

# Verify the Application

Open:

```
http://localhost:8080
```

or

```bash
curl http://localhost:8080
```

---

# Stop and Remove Container

```bash
docker stop container1
docker rm container1

docker stop container2
docker rm container2
```

---

# Image Size Comparison

| Lab | Build Method | Base Image | Expected Size |
|------|--------------|------------|---------------|
| Lab 3 | Build inside Docker | Maven + Java 17 | Large (~1 GB+) |
| Lab 4 | Build outside Docker | Java 17 Runtime | Much Smaller (~200–300 MB) |

> The exact image size depends on the Docker image version.
<img width="875" height="95" alt="image" src="https://github.com/user-attachments/assets/1461ecbf-94b6-4584-b792-d202a88d9476" />


---

---

# Lab 5 - Multi-Stage Docker Build

### Objective

Build and package the Spring Boot application using a multi-stage Docker build to reduce the final image size.

### Build Stage

- Maven
- Java 17

### Runtime Stage

- Java 17 Runtime

### Steps

- Copy the application source code into the build stage
- Build the application using Maven
- Copy only the generated JAR into the runtime stage
- Expose port 8080
- Run the application

### Build Image

```bash
docker build -f Dockerfile.lab5 -t app3 .
```

### Run Container

```bash
docker run -d --name container3 -p 8080:8080 app3
```

---

# Lab 6 - Managing Docker Environment Variables

### Objective

Containerize a Python Flask application and manage environment variables using different Docker approaches.

### Base Image

- Python 3
- Flask

### Steps

- Build the Docker image
- Configure environment variables using:
  - `docker run -e`
  - `--env-file`
  - `ENV` in the Dockerfile
- Expose port 8080
- Run the Flask application

### Build Image

```bash
docker build -f Dockerfile.lab6 -t app4 .
```

### Run Development Container

```bash
docker run -d \
--name app-dev \
-p 8081:8080 \
-e APP_MODE=development \
-e APP_REGION=us-east \
app4
```

### Run Staging Container

```bash
docker run -d \
--name app-stage \
-p 8082:8080 \
--env-file staging.env \
app4
```

### Run Production Container

```bash
docker run -d \
--name app-prod \
-p 8083:8080 \
app4
```

---
# Lab 7 - Docker Volumes & Bind Mounts

### Objective

Learn how to persist container data using Docker Volumes and share local files with containers using Bind Mounts.

### Steps

- Create a Docker volume for Nginx logs.
- Create a local `index.html`.
- Run an Nginx container.
- Mount a Docker Volume to `/var/log/nginx`.
- Mount the local HTML directory to `/usr/share/nginx/html`.
- Verify the web page.
- Modify the local HTML file.
- Verify the changes without rebuilding the container.
- Confirm logs are stored inside the Docker volume.

### Create Volume

```bash
docker volume create nginx_logs
```

### Run Container

```bash
docker run -d \
--name nginx \
-p 8080:80 \
-v nginx_logs:/var/log/nginx \
-v $(pwd)/html:/usr/share/nginx/html \
nginx
```

### Verify

Open

```
http://localhost:8080
```

or

```bash
curl http://localhost:8080
```

Modify `index.html` locally and refresh the browser.

### Verify Logs

```bash
docker volume inspect nginx_logs
```

---

# Lab 8 - Docker Networking for Microservices

### Objective

Create a custom Docker bridge network and verify communication between frontend and backend containers.

### Steps

- Build frontend image.
- Build backend image.
- Create a custom Docker network.
- Run backend container.
- Run frontend1 on the custom network.
- Run frontend2 on the default bridge network.
- Verify container communication.
- Remove the network after testing.

### Create Network

```bash
docker network create \
--subnet=192.168.10.0/24 \
ivolve-network
```

### Build Images

```bash
docker build -t frontend ./frontend

docker build -t backend .
```

### Run Backend

```bash
docker run -d \
--name backend \
--network ivolve-network \
backend
```

### Run Frontend

```bash
docker run -d \
--name frontend1 \
--network ivolve-network \
frontend
```

### Verify Network

```bash
docker network inspect ivolve-network
```

---

# Lab 9 - Docker Compose Application Stack

### Objective

Deploy a multi-container Node.js and MySQL application using Docker Compose.

### Services

- Node.js Application
- MySQL Database

### Features

- Docker Compose
- Named Volumes
- Environment Variables
- Health Checks
- Persistent Database Storage

### Start Application

```bash
docker compose up -d
```

### Verify Running Containers

```bash
docker compose ps
```

### Verify Application

```
http://localhost:3000
```

### Health Endpoint

```
http://localhost:3000/health
```

### Readiness Endpoint

```
http://localhost:3000/ready
```

### View Logs

```bash
docker compose logs
```

### Stop Application

```bash
docker compose down
```
---

# Image Size Comparison

| Lab | Build Method | Base Image | Expected Size |
|------|--------------|------------|---------------|
| Lab 3 | Build inside Docker | Maven + Java 17 | Large (~1 GB+) |
| Lab 4 | Build outside Docker | Java 17 Runtime | Much Smaller (~200–300 MB) |
| Lab 5 | Multi-stage Build | Maven + Java Runtime | Optimized (~200–300 MB) |
| Lab 6 | Python Flask | Python 3 | Small (~150–250 MB) |
| Lab 7 | Official Nginx Image | nginx | Official Image |
| Lab 8 | Python Microservices | Python 3 | Small |
| Lab 9 | Docker Compose Stack | Node.js + MySQL | Multi-container |

> The exact image size depends on the Docker image version.

---

# Technologies Used

- Docker
- Docker Compose
- Docker Volumes
- Bind Mounts
- Docker Networks
- Dockerfile
- Multi-stage Builds
- Environment Variables
- Spring Boot
- Java 17
- Maven
- Python
- Flask
- Node.js
- MySQL
- Nginx

---

# Key Learning Outcomes

- Writing Dockerfiles for Java and Python applications.
- Building Spring Boot applications inside Docker.
- Running pre-built JAR files in containers.
- Creating optimized images using multi-stage builds.
- Managing environment variables during build and runtime.
- Understanding the impact of base images on Docker image size.
- Comparing different Docker image optimization techniques.
- Enabling communication between multiple containers.
- Deploying complete application stacks using Docker Compose.
- Working with health checks and persistent storage.
