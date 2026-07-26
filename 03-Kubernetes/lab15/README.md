# Kubernetes Lab 15 – Deploy Node.js Application with ConfigMap, Secret & Persistent Storage

## 📌 Overview

This lab demonstrates how to deploy a Node.js application on Kubernetes while integrating multiple Kubernetes resources together.

The application retrieves its configuration from a ConfigMap, sensitive credentials from a Secret, stores logs using a PersistentVolumeClaim, and connects to a MySQL database running as a StatefulSet through a Headless Service.

---

## 🎯 Objectives

- Deploy a Node.js application using a Deployment.
- Expose the application using a ClusterIP Service.
- Inject environment variables from ConfigMap and Secret.
- Mount a PersistentVolumeClaim for application logs.
- Connect the application to a MySQL StatefulSet.
- Verify communication between application and database.
- Access the application using `kubectl port-forward`.

---

## 🏗️ Architecture

```text
                   +--------------------+
                   |    ConfigMap       |
                   +--------------------+
                            |
                            |
                   +--------------------+
                   |      Secret        |
                   +--------------------+
                            |
                            |
                    +------------------+
                    | Node.js Pods     |
                    | Deployment (x2)  |
                    +------------------+
                      |            |
                      |            |
              app-logs PVC     ClusterIP Service
                      |            |
                      |            |
             +-----------------------------+
             |      nodejs-service         |
             +-----------------------------+
                            |
                            |
                     Port Forward
                            |
                     http://localhost:3000

                            |
                            |
                   mysql (Headless Service)
                            |
                    MySQL StatefulSet
                            |
                    mysql-data-pvc
                            |
                    mysql-data-pv
```

---

## 📂 Project Structure

```
lab15/
├── configmap.yaml
├── deployment.yaml
├── pvc.yaml
├── pv.yaml
├── secret.yaml
├── service.yaml
└── README.md
```

---

## 🚀 Deployment Steps

### 1. Create Persistent Storage

```bash
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
```

---

### 2. Create ConfigMap & Secret

```bash
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
```

---

### 3. Deploy the Application

```bash
kubectl apply -f deployment.yaml
```

---

### 4. Create the Service

```bash
kubectl apply -f service.yaml
```

---

### 5. Verify Resources

```bash
kubectl get pods -n ivolve
kubectl get svc -n ivolve
kubectl get pvc -n ivolve
kubectl get pv
```

---

### 6. Access the Application

```bash
kubectl port-forward svc/nodejs-service 3000:3000 -n ivolve
```

Open:

```
http://localhost:3000
```

---

## ✅ Verification

Application logs:

```text
✅ Connected to MySQL and 'ivolve' DB found.
🚀 Server started on http://0.0.0.0:3000
```

Deployment:

```bash
kubectl get deployment -n ivolve
```

Pods:

```bash
kubectl get pods -n ivolve
```

Services:

```bash
kubectl get svc -n ivolve
```

---

## 🐞 Troubleshooting

### PVC stuck in Pending

Cause:

```
storageClassName does not match
```

Solution:

```yaml
storageClassName: ""
```

when using a manually created PersistentVolume.

---

### MySQL authentication failed

Cause:

```
Access denied for user
```

Solution:

- Verify Secret values.
- Verify MySQL user creation.
- Ensure ConfigMap matches database credentials.

---

### Database not found

Cause:

```
Database not found
```

Solution:

Create the expected database or update the application configuration to match the existing database name.

---

### DNS resolution failed

Cause:

```
getaddrinfo EAI_AGAIN mysql
```

Solution:

Ensure the MySQL Headless Service exists:

```bash
kubectl apply -f ../lab14/mysql-headless-service.yaml
```

Verify:

```bash
kubectl get svc -n ivolve
kubectl get endpoints mysql -n ivolve
```

---

## 💡 Key Learnings

- Deploying applications using Kubernetes Deployments.
- Using ConfigMaps for application configuration.
- Securing sensitive data with Secrets.
- Mounting PersistentVolumeClaims inside containers.
- Connecting applications to StatefulSets.
- Understanding Headless Services and Kubernetes DNS.
- Debugging Kubernetes using logs, events, and describe commands.
- Troubleshooting PersistentVolume binding issues.
- Managing application configuration without rebuilding container images.

---

## 🛠 Technologies Used

- Kubernetes
- Docker
- Node.js
- MySQL
- ConfigMap
- Secret
- PersistentVolume
- PersistentVolumeClaim
- Deployment
- StatefulSet
- ClusterIP Service
- Headless Service

---

## 🎉 Lab Status

✅ Completed Successfully
