# Lab 14: Deploying MySQL with StatefulSet

## 📌 Overview

This lab demonstrates how to deploy a **stateful MySQL database** on Kubernetes using a **StatefulSet** with persistent storage.

Unlike a Deployment, a StatefulSet provides:

- Stable Pod names
- Persistent storage
- Ordered deployment and termination
- Predictable network identity

The deployment also integrates resources created in previous labs, including:

- ConfigMap
- Secret
- PersistentVolume (PV)
- PersistentVolumeClaim (PVC)

---

## 🎯 Objectives

- Deploy MySQL using a StatefulSet
- Create a Headless Service
- Configure MySQL using Kubernetes Secrets
- Persist database data using a PersistentVolumeClaim
- Verify successful deployment

---

## 📂 Files

```
lab14/
├── mysql-headless-service.yaml
└── mysql-statefulset.yaml
```

---

## 🏗 Architecture

```
                   +--------------------+
                   |   Headless Service |
                   |   mysql            |
                   +---------+----------+
                             |
                             |
                     mysql-0 Stateful Pod
                             |
              +--------------+--------------+
              |                             |
        Kubernetes Secret             PersistentVolumeClaim
     (MYSQL_ROOT_PASSWORD)             (app-logs-pvc)
                                             |
                                             |
                                     PersistentVolume
                                             |
                                             |
                                      HostPath Storage
```

---

## 📄 Headless Service

The Headless Service provides stable DNS records for the StatefulSet Pod.

```
mysql-0.mysql
```

Unlike a normal Service, it does **not** receive a ClusterIP.

```yaml
clusterIP: None
```

---

## 📄 StatefulSet

The StatefulSet deploys a single MySQL instance with:

- MySQL 8.0
- Stable Pod name (`mysql-0`)
- Persistent storage
- Secret-based root password
- Database initialization

---

## 🔐 Secret Integration

The root password is injected securely using Kubernetes Secret.

```yaml
env:
- name: MYSQL_ROOT_PASSWORD
  valueFrom:
    secretKeyRef:
      name: mysql-secret
      key: MYSQL_ROOT_PASSWORD
```

---

## 💾 Persistent Storage

The MySQL data directory is mounted from the existing PVC.

```yaml
volumeMounts:
- name: mysql-storage
  mountPath: /var/lib/mysql
```

PVC used:

```
app-logs-pvc
```

---

## 🚀 Deployment

Deploy the Headless Service:

```bash
kubectl apply -f mysql-headless-service.yaml
```

Deploy the StatefulSet:

```bash
kubectl apply -f mysql-statefulset.yaml
```

---

## ✅ Verification

Check the Service:

```bash
kubectl get svc -n ivolve
```

Expected:

```text
NAME    TYPE        CLUSTER-IP   PORT(S)
mysql   ClusterIP   None         3306/TCP
```

---

Check the StatefulSet:

```bash
kubectl get statefulset -n ivolve
```

---

Check the Pod:

```bash
kubectl get pods -n ivolve
```

Expected:

```text
mysql-0   Running
```

---

Connect to the container:

```bash
kubectl exec -it mysql-0 -n ivolve -- bash
```

Login to MySQL:

```bash
mysql -uroot -p
```

Show databases:

```sql
SHOW DATABASES;
```

Expected output:

```text
iVolve
information_schema
mysql
performance_schema
sys
```
<img width="1238" height="646" alt="image" src="https://github.com/user-attachments/assets/235d3eba-f95f-4a3c-baf4-fcb0784280bb" />

---


## ⚠ Troubleshooting

### ResourceQuota prevented Pod creation

Initially, the StatefulSet failed because the namespace had a ResourceQuota allowing only **2 Pods**.

Error:

```text
pods "mysql-0" is forbidden:
exceeded quota
```
<img width="1172" height="306" alt="image" src="https://github.com/user-attachments/assets/5780bd01-41af-4332-92e4-6f84ce080fe1" />



The issue was resolved by increasing the Pod quota and reapplying the ResourceQuota.

---

### Persistent Storage

The MySQL Pod uses the existing PersistentVolumeClaim created in the previous lab.

Database files remain available even if the Pod is deleted.

---

## 📚 Key Concepts Learned

- StatefulSet
- Headless Service
- Stable Pod Identity
- Persistent Storage
- PersistentVolumeClaim
- Kubernetes Secret
- Environment Variables
- ResourceQuota Troubleshooting
- MySQL Initialization
- Stateful Applications on Kubernetes

---

## 🎓 What I Learned

- When to use StatefulSet instead of Deployment
- How Headless Services provide stable DNS for Stateful applications
- How Kubernetes Secrets securely inject sensitive data
- How PersistentVolumes preserve data across Pod recreation
- How ResourceQuotas can prevent workload scheduling
- How to troubleshoot StatefulSet deployment issues using `kubectl describe`
