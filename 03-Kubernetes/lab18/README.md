# Lab 18 – Control Pod-to-Pod Traffic Using NetworkPolicy

## Overview

In this lab, I implemented a Kubernetes **NetworkPolicy** to secure communication between application components. The objective was to allow only the Node.js application pods to access the MySQL database on the default MySQL port (**3306**) while denying access from all other pods.

---

## Objectives

* Create a NetworkPolicy named `allow-app-to-mysql`
* Target MySQL pods using labels
* Allow ingress traffic only from the Node.js application pods
* Restrict communication to TCP port **3306**
* Verify the applied policy

---

## Environment

* Kubernetes
* Docker Desktop Kubernetes Cluster
* Namespace: `ivolve`
* MySQL StatefulSet
* Node.js Deployment
* Headless MySQL Service

---

## Verify Pod Labels

```bash
kubectl get pods -n ivolve --show-labels
```

Verified labels:

| Pod     | Label            |
| ------- | ---------------- |
| MySQL   | `app=mysql`      |
| Node.js | `app=nodejs-app` |

---

## Create NetworkPolicy

**networkpolicy.yaml**

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app-to-mysql
  namespace: ivolve

spec:
  podSelector:
    matchLabels:
      app: mysql

  policyTypes:
    - Ingress

  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: nodejs-app
      ports:
        - protocol: TCP
          port: 3306
```

---

## Apply the Policy

```bash
kubectl apply -f networkpolicy.yaml
```

---

## Verify the NetworkPolicy

```bash
kubectl get networkpolicy -n ivolve
```
<img width="880" height="60" alt="image" src="https://github.com/user-attachments/assets/6482583c-4a8e-4585-997b-7c43706359b0" />


```bash
kubectl describe networkpolicy allow-app-to-mysql -n ivolve
```
<img width="970" height="216" alt="image" src="https://github.com/user-attachments/assets/68b3a79d-d5be-44d9-9e24-288b70ad65ab" />


Verification confirmed:

* Target Pods: `app=mysql`
* Allowed Source: `app=nodejs-app`
* Allowed Port: `3306/TCP`
* Policy Type: `Ingress`

---

## Verify Kubernetes Service

```bash
kubectl get svc -n ivolve
```

The MySQL service is exposed as a **Headless Service**.

```bash
kubectl describe svc mysql -n ivolve
```

Verified:

* Service selector matches the MySQL pod.
* Endpoint points to the MySQL StatefulSet pod.
* Port 3306 is exposed correctly.

---

## Validate Database Connectivity

Verified that the Node.js application successfully connected to MySQL by checking the application logs.

```bash
kubectl logs <nodejs-pod> -n ivolve
```
<img width="984" height="76" alt="image" src="https://github.com/user-attachments/assets/de3cfc6c-179f-4b1b-be70-54c0a67cee67" />


Application output:

```text
Attempting to connect to MySQL...
Connected to MySQL and 'ivolve' DB found.
Server started on http://0.0.0.0:3000
```
<img width="986" height="159" alt="image" src="https://github.com/user-attachments/assets/b2d694f7-3018-4aa5-80dc-6a22fa7a75b8" />

---

## Challenges

While validating connectivity, I attempted to test TCP connectivity using `nc` (netcat). The command returned a timeout even though:

* Kubernetes DNS resolved the MySQL service correctly.
* The MySQL service had a valid endpoint.
* MySQL was listening on port 3306.
* The application logs confirmed a successful database connection.

Since the application communicated with the database successfully, the deployment and service configuration were verified as working correctly. The timeout observed with `nc` appears to be related to the testing environment rather than the application itself.

---

## Key Concepts Learned

* Kubernetes NetworkPolicy
* Ingress Traffic Control
* Pod Selector
* Label-based Access Control
* Headless Services
* Kubernetes DNS
* Service Endpoints
* Principle of Least Privilege
* Zero Trust Networking

---

## Project Structure

```text
lab18/
├── networkpolicy.yaml
└── README.md
```

---

## Screenshots

* Pod labels
* NetworkPolicy creation
* NetworkPolicy description
* MySQL Service
* Application logs showing successful database connection
