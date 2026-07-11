# Lab 07 – Docker Volumes & Bind Mounts with Nginx

## 📌 Overview

This lab demonstrates how to use **Docker Volumes** and **Bind Mounts** with an Nginx container.

- **Docker Volume** is used to persist container data independently of the container lifecycle.
- **Bind Mount** allows serving files directly from the host machine, making it ideal for development where file changes are reflected instantly inside the container.

---

## 🎯 Objectives

- Create a Docker volume for Nginx logs.
- Create a custom HTML page on the host machine.
- Mount the HTML directory into the Nginx container using a Bind Mount.
- Mount a Docker Volume for Nginx log storage.
- Verify the web page using `curl`.
- Modify the HTML file without rebuilding or restarting the container.
- Verify the mounted volume and bind mount.
- Remove the created Docker resources.

---

## 📁 Project Structure

```text
lab07/
├── README.md
└── nginx-bind/
    └── html/
        └── index.html
```

---

## 🚀 Step 1: Create Docker Volume

```bash
docker volume create nginx_logs
```

Verify the volume:

```bash
docker volume ls
docker volume inspect nginx_logs
```

---

## 🚀 Step 2: Create Host Directory

```bash
mkdir -p nginx-bind/html
```

Create a custom web page:

```bash
echo "Hello from Bind Mount" > nginx-bind/html/index.html
```

---

## 🚀 Step 3: Run Nginx Container

```bash
docker run -d \
  --name nginx \
  -p 8080:80 \
  -v nginx_logs:/var/log/nginx \
  -v "$(pwd)/html:/usr/share/nginx/html" \
  nginx
```

---

## 🚀 Step 4: Verify the Website

```bash
curl localhost:8080
```

Expected output:

```text
Hello from Bind Mount
```

---

## 🚀 Step 5: Modify the HTML File

```bash
echo "Hello Momen" > html/index.html
```

Verify again:

```bash
curl localhost:8080
```

Expected output:

```text
Hello Momen
```

This confirms that the **Bind Mount** updates are reflected immediately without rebuilding or restarting the container.

---

## 🚀 Step 6: Verify Mounted Resources

Verify Docker mounts:

```bash
docker inspect nginx --format '{{json .Mounts}}'
```

Example output:

```text
Volume  -> /var/log/nginx
Bind    -> /usr/share/nginx/html
```

---

## 🚀 Step 7: Verify Nginx Logs

Display log files:

```bash
docker exec nginx ls -l /var/log/nginx
```

> **Note**
>
> The official Nginx Docker image redirects `access.log` and `error.log` to `stdout` and `stderr`. Therefore, the log files appear as symbolic links:
>
> ```text
> access.log -> /dev/stdout
> error.log  -> /dev/stderr
> ```
>
> Container logs can be viewed using:
>
> ```bash
> docker logs nginx
> ```

---

## 🧹 Cleanup

```bash
docker stop nginx

docker rm nginx

docker volume rm nginx_logs
```

---

## 📚 Key Concepts

- Docker Volumes provide persistent storage managed by Docker.
- Bind Mounts share files directly between the host and the container.
- Bind Mounts are ideal for development because changes on the host are reflected instantly.
- Official Nginx Docker images forward logs to Docker logging (`stdout`/`stderr`) instead of writing directly to log files.
- Docker volumes remain available even after containers are removed until explicitly deleted.

---

## 🛠 Technologies Used

- Docker
- Docker Volumes
- Bind Mounts
- Nginx
- Linux
