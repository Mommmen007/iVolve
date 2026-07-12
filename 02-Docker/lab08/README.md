# Lab 8: Custom Docker Network for Microservices

## 📌 Overview

This lab demonstrates how to create a custom Docker bridge network to enable communication between multiple microservices. Two Python Flask applications (Frontend and Backend) are containerized and deployed on different Docker networks to verify container-to-container communication using Docker DNS.

---

## 🎯 Objectives

- Create Docker images for frontend and backend services.
- Create a custom Docker bridge network.
- Connect selected containers to the custom network.
- Verify communication between containers using Docker DNS.
- Compare communication behavior between custom and default bridge networks.
- Remove the custom network after testing.

---

## 📁 Project Structure

```text
lab08/
├── backend/
│   ├── app.py
│   └── Dockerfile
│
└── frontend/
    ├── app.py
    ├── requirements.txt
    └── Dockerfile
```

---

## 🐳 Frontend Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python","app.py"]
```

---

## 🐳 Backend Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY . .

RUN pip install --no-cache-dir flask

EXPOSE 5000

CMD ["python","app.py"]
```

---

## 🚀 Build Docker Images

### Frontend

```bash
cd frontend
docker build -t frontend-app .
```

### Backend

```bash
cd ../backend
docker build -t backend-app .
```

---

## 🌐 Create Custom Network

```bash
docker network create \
--driver bridge \
--subnet=192.168.10.0/24 \
ivolve-network
```

Verify:

```bash
docker network inspect ivolve-network
```

---

## ▶️ Run Containers

### Backend

```bash
docker run -d \
--name backend \
--network ivolve-network \
backend-app
```

### Frontend 1 (Custom Network)

```bash
docker run -d \
--name frontend1 \
--network ivolve-network \
-p 5001:5000 \
frontend-app
```

### Frontend 2 (Default Bridge)

```bash
docker run -d \
--name frontend2 \
-p 5002:5000 \
frontend-app
```

---

## ✅ Verification

List running containers:

```bash
docker ps
```

Inspect custom network:

```bash
docker network inspect ivolve-network
```

Expected:

- `backend` connected to `ivolve-network`
- `frontend1` connected to `ivolve-network`
- `frontend2` connected to the default `bridge` network

---

## 🌍 Test the Applications

Frontend connected to the custom network:

```
http://localhost:5001
```

Expected output:

```
Frontend received: Hello from Backend!
```

Frontend connected to the default bridge:

```
http://localhost:5002
```

Expected output:

```
Could not connect to backend.
```

This confirms that Docker's embedded DNS resolves the backend container only for containers sharing the same custom network.

---

## 🧹 Cleanup

Remove containers:

```bash
docker rm -f frontend1 frontend2 backend
```

Remove network:

```bash
docker network rm ivolve-network
```

---

## 📚 Key Concepts Learned

- Docker bridge networks
- Custom Docker networks
- Docker embedded DNS
- Container-to-container communication
- Service discovery using container names
- Network isolation between containers

---

## 🛠 Technologies Used

- Docker
- Docker Networking
- Python 3
- Flask
- Requests Library

---

## 👨‍💻 Author

**Momen Elgedawy**

Cloud & DevOps Engineer
