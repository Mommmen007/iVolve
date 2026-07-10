# Docker Labs - Spring Boot Containerization

This repository contains **Lab 3** and **Lab 4** from the Cloud DevOps Accelerator program. The goal is to containerize a Java Spring Boot application using different Docker approaches and compare the resulting image sizes.

## Repository Structure

```text
.
├── Dockerfile.lab3
├── Dockerfile.lab4
├── src/
├── pom.xml
└── README.md
```

> If you are using a single Dockerfile, rename it according to the lab before building.

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

---

# Technologies Used

- Docker
- Java 17
- Spring Boot
- Maven

---

# Key Learning Outcomes

- Writing Dockerfiles for Java applications.
- Building Spring Boot applications inside Docker.
- Running pre-built JAR files in containers.
- Understanding the impact of base images on Docker image size.
- Comparing Maven-based images with Java Runtime images.
