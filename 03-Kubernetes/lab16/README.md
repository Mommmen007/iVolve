# Lab 16 – Kubernetes Init Container for Pre-Deployment Database Setup

## 📌 Overview

In this lab, I enhanced the existing Node.js Deployment by adding an **Init Container**. The Init Container executes before the application container starts and is responsible for preparing the MySQL database environment.

It connects to the MySQL server, creates the required database if it does not already exist, and grants the application user the necessary privileges. Kubernetes ensures that the main application container starts only after the Init Container completes successfully.

---

## 🎯 Objectives

* Modify the existing Node.js Deployment.
* Add an Init Container using the **mysql:5.7** image.
* Read database connection information from ConfigMap and Secret.
* Create the **ivolve** database automatically.
* Grant the application user full privileges on the database.
* Verify the database and permissions manually.

---

## 📁 Project Structure

```text
lab16/
├── deployment.yaml
├── service.yaml
└── README.md
```

---

## ⚙️ Technologies Used

* Kubernetes
* MySQL 5.7
* ConfigMap
* Secret
* Init Containers
* kubectl

---

## 🚀 Implementation Steps

### 1. Copy the Deployment from Lab 15

The existing Node.js Deployment from Lab 15 was reused as the starting point.

---

### 2. Add an Init Container

An Init Container was added before the main application container.

Its responsibilities are:

* Connect to the MySQL server.
* Create the **ivolve** database.
* Grant the application user full privileges.
* Exit successfully before allowing the Node.js container to start.

The Init Container uses the **mysql:5.7** client image.

---

### 3. Configure Environment Variables

Database connection values are loaded from existing Kubernetes resources.

**ConfigMap**

* DB_HOST
* DB_USER

**Secret**

* DB_PASSWORD
* MYSQL_ROOT_PASSWORD

This keeps sensitive information outside the Deployment manifest.

---

### 4. Apply the Deployment

```bash
kubectl apply -f deployment.yaml
```

---

### 5. Verify Pod Status

```bash
kubectl get pods -n ivolve
```

The Node.js Pods should reach the **Running** state after the Init Container completes successfully.


<img width="714" height="149" alt="image" src="https://github.com/user-attachments/assets/2c13658c-ee98-4ed1-a50e-f9ad6891352d" />

---

### 6. View Init Container Logs

```bash
kubectl logs <nodejs-pod> -c init-db -n ivolve
```

The only output displayed was:

```text
mysql: [Warning] Using a password on the command line interface can be insecure.
```

<img width="1053" height="182" alt="image" src="https://github.com/user-attachments/assets/b704a6b1-a6f7-45fe-a241-bf5d4f3e4f7b" />


This is a MySQL warning, **not an error**, indicating that the SQL commands executed successfully.

---

### 7. Verify Database Creation

Connect to the MySQL Pod:

```bash
kubectl exec -it mysql-0 -n ivolve -- bash
```

Login:

```bash
mysql -uroot -p
```

Verify the database:

```sql
SHOW DATABASES;
```

Output included:

```text
ivolve
```

<img width="293" height="217" alt="image" src="https://github.com/user-attachments/assets/3ea8ddf2-7205-4933-a4ee-49a80286be30" />

---

### 8. Verify User Permissions

List MySQL users:

```sql
SELECT user, host FROM mysql.user;
```

Verify privileges:

```sql
SHOW GRANTS FOR 'appuser'@'%';
```

Output confirmed that **appuser** has full privileges on the **ivolve** database.

<img width="554" height="176" alt="image" src="https://github.com/user-attachments/assets/db37cda3-efc3-4bf1-92d4-56d70872da93" />

---

## 📷 Verification

The following verifications were completed successfully:

* ✅ Init Container executed before the application container.
* ✅ Node.js Pods started successfully.
* ✅ Database **ivolve** was created automatically.
* ✅ Application user exists.
* ✅ Required database privileges were granted.
* ✅ MySQL verification completed successfully.

---

## 💡 Key Learning Points

* Init Containers always run before application containers.
* The main container starts only after all Init Containers finish successfully.
* Init Containers are ideal for initialization tasks such as database setup, waiting for dependencies, and configuration.
* ConfigMaps and Secrets provide configuration and sensitive credentials without hardcoding values.
* Using `CREATE DATABASE IF NOT EXISTS` makes the initialization process safe to run multiple times.

---

## ✅ Result

The Node.js application was successfully deployed using an Init Container that automatically prepared the MySQL environment by creating the required database and assigning the appropriate permissions before the application started. This demonstrates a reliable Kubernetes deployment pattern for initializing application dependencies.
